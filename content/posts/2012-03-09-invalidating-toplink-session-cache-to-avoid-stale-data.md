+++
draft       = false
date        = "2012-03-09T18:42:00+00:00"
lastmod     = "2017-02-22"
title       = "Invalidating Toplink Session Cache To Avoid Stale Data"
categories  = ["Java"]
tags        = ["oracle", "java", "toplink", "oracle application server", "weblogic"]
+++

Toplink is a Java framework that maps objects to database tables. It is the reference implementation for the Java Persistence Architecture, or JPA. Developers use Toplink to manage data persistence (storage), queries, and transactions in an Oracle database. By default, Toplink makes use of a special cache called a Session Cache, maintained on the server, which is meant to speed up performance. Queried data is stored in the Session Cache and used in subsequent queries to reduce or eliminate calls to the database.

One problem with this approach is how to deal with "stale data". In other words, what happens when data is changing in the database? How does the developer ensure that the cached data is current?

I ran into this issue recently when consulting with one of our clients on a third-party J2EE application that used Toplink for its persistence layer. What we noticed was that at a certain point in the business process the application was clearly displaying stale data. The "status" column on a certain record was being changed in the database (as could be verified by a SQL query), but the application still displayed the prior status. Refreshing the screen and requerying the data had no effect. The problem was that the status was being changed through a custom stored procedure which performed direct updates on the screen's underlying table. This approach was technically against the directives of the third-party application vendor, who insisted that all such changes to the database be made via calls to its own web services. That was for good reason. The web services they provided as an API for custom development were the same ones used by the screens to query data. They used the same Toplink Session Cache. Changes made through the web services, therefore, would have updated the cache, thereby avoiding the problem. As it was, the client had unintentionally created out-of-sync scenario between the cache and the database, and the status information displayed on the screen was invalid.

The most obvious solution would have been to modify the custom procedure to make use of the provided APIs. However, we didn't have an API in this instance that provided exactly the required functionality. Having the software vendor modify or extend the API for our purposes would have been prohibitively expensive, in both time and money.

Another possibility we considered was to have the vendor make a smaller modification to bypass the Toplink cache for queries on that screen. It is possible to disable caching per table, through the global configuration file persistence.xml, or as a property of an individual query. However, the vendor was not predisposed to introduce what they viewed as a "hack" to accommodate our custom functionality.

Ultimately we decided to implement a tacked-on solution that would arrive at the same goal and eliminate the need for an additional iteration through the vendor's dev/test cycle. We created a custom servlet, installed in the third-party application itself, that invalidated the Toplink Session Cache for a specified table or, as an additional method, for all tables. Invalidating the cache means that the next time a query is executed against that table, the  cache is bypassed and data is retrieved directly from the database. To make it easier for the non-Java in-house developers to use, we created a custom wrapper package to mask the call to the servlet. A call to this package was added to the client's custom code just after the record in question was updated and commited.

The solution consisted of two Java classes, JAR'ed and deployed as a library in the third-party application's WEB-INF/lib directory.

CacheInvalidator.java  
CacheInvalidatorServlet.java

The web application configuration file web.xml was also modified to add a reference to the new "cacheinvalidator" servlet.

The code was adapted from the following documentation, available on Oracle Tech Net:

[Database Change Notification and TopLink Cache Invalidation](http://www.oracle.com/technetwork/middleware/ias/index-097190.html)  
[Oracle TopLink Cache Invalidation](http://www.oracle.com/technetwork/middleware/ias/index-100679.html)

The two methods of CacheInvalidator that do the actual work are shown here. The first takes tablename as an argument, so that the invalidation can be targeted. The second invalidates, or effectively wipes, the entire cache.

```java
public void invalidateClass(Session session, String tableName) throws Exception { 
    if(tableName == null || tableName.trim().equals("")) { 
        throw new Exception("tableName cannot be null"); 
    } 
    Class baseClass = (Class)tableNameToClass.get(tableName); 
    if(baseClass == null) { 
        throw new Exception("table \"" + tableName + "\" not found in cache"); 
    } 
 
    // invalidate in TopLink cache the object corresponding to the baseClass 
    session.getIdentityMapAccessor().invalidateClass(baseClass); 
} 
 
// invalidate all objects in the cache 
public void invalidateAllClasses(Session session) { 
    // invalidate in TopLink cache the object corresponding to the baseClass 
    session.getIdentityMapAccessor().invalidateAll(); 
} 
```

The CacheInvalidatorServlet takes a table name or the special keyword "ALL" as a parameter and calls the corresponding method in CacheInvalidator.

```java
String oName = request.getParameter("objectName"); 
String responseString = ""; 

try { 
    SessionManager manager = SessionManager.getManager(); 
    //SESSIONNAME defined in sessions.xml 
    Server server = (Server)manager.getSession("SESSIONNAME"); 
    Session session = server.getActiveSession(); 

    CacheInvalidator invalidator = new CacheInvalidator(session); 
    if (oName.equals("ALL")) { 
        invalidator.invalidateAllClasses(session); 
        responseString = "successfully invalidated toplink cache for all tables."; 
    } else { 
        invalidator.invalidateClass(session, oName); 
        responseString = "successfully invalidated toplink cache for table: "+oName; 
    } 
} catch (Exception e) { 
    responseString = e.getMessage(); 
} 
```

Calling a servlet from PL/SQL is not particularly complicated, but in our case involved the lookup of server information stored in the database. The wrapper package allowed us to provide the developers with a simple, one-line invocation.

```sql
p_servlet_response := utl_http.request('http://'||l_app_server_host||':'
          ||l_app_server_port||'/jedi/cacheinvalidatorservlet?objectName='
          ||p_obj_to_invalidate);
```

After making a call to our cacheinvalidator package subsequent to updating the status record, the stale data problem was eliminated in the application.

