# mod-plsql
A java servlet to replace deprecated apache mod_plsql cartridge

Created by Ray DeBruyn
ray@eis.ca

Enterprise Information Systems
880 Taylor Creek dr
Ottawa, On Canada
K4A 0Z9

Jim Miles - President
jim@eis.ca

Contact me:
- if you would like help adapting the code to meet you specific needs. I can be hired to do so.

I wasn't interested in building a full ords.jar functionality. We have used pl/sql web toolkit for over 25 years. We were on a path to upgrade our servers and couldn't install the older version of mod_plsql. Oracle had deprecated it in the 11g release. I needed a solution to be able to continue using the existing code.

There are a few solutions out there, but any one required modification to run my code. Many were overly complicated for my taste. I prefer a simple approach and highly readable code.

See the wiki for a brief note on the history of why I needed to write the servlet as well as where the code is now and my plan moving forward.

I wrote this for my purposes, so the code includes fixes for my specific issues as well as some code that is custom to my environment. I'll try to lay out the approach here as well as describing any fixes or customizations. I'm just giving the gyst, so there is no object to call the method from or parameters shown.

The classes are:
Controller - the servlet
Dad - a class to represent a database access descriptor
ParameterDBMeta - the constructor uses the packageName, procedureName and parameterName to lookup the datatype. It also 
    has a collection of parameterValues
ConnectionPool - has an collection of oracle.ucp.jdbc.PoolDataSource, one for each Dad, to handle connection pooling
DadManager - where the main code runs. 

There are 3 main methods. There are in the DadManager class - initialSetup, setParameters and showPage (or showFile if doing a file download). initialSetup and setParameters each return true or false. The Controller servlet calls them like so:
if (initialSetup())
{
    if (setParameters())
    {
        showPage();
    }
}

initialSetup:
gets the dadName, packageName and procedureName or, potentially the fileName if the path is docs.
NOTE: this code assumes that images are always fetched using the path of "docs". This is not set up per Dad
It then checks if a PoolDataSource has been added to the collection for the dad. If so, gets it from the collection, If not, constructs a new one, adds it to the collection and uses it.
It then attempts to get a connection from the PoolDataSource.
It also adds the headers to a collection.
If everything is good and the connection is found, it returns true, otherwise it returns false.
If true, we can proceed.

setParameters:
if the fileName was set from the docs path, sets a parameter for p_filename with the value of fileName and returns true

Otherwise has 2 possible courses. If the request is multipart for file uploads or not.
Both loop through each parameter value. If the ParameterDBMeta is created for that parameter, uses it, else creates a new on and adds it to the collection. The it adds the parameter value to the ParameterDBMeta's parameterValues collection.
If the request is multipart, it saves the file to the images table, returns the file name and adds thet parameter to  to the ParameterDBMeta's parameterValues collection.
NOTE: In my case, I do not define the docs table in the Dad. You will have to adjust the code if you use different tables in different schemas. There is a plsql block defined to insert the file that would have to be modified. I also have my own function to create the distinct filename. You may want to create your own.
if anything fails, it shows the error page, saves the error and returns false.
otherwise it retuns true and proceeds.

showPage:
It adds response headers to the page
NOTE: if you use headers in your plsql code, you may want to review the headers to ensure the ones you require are added and that the values are what you expect
Then gets the plsql block to execute.
NOTE: I made some modifications to what mod_plsql would produce. I save the session info for all database hits, so I have some p_controller calls that you will want to remove or recreate. I also try to verify that the package and procedure exist. This is to avoid sql injection.
The plsql block uses bind variables, so I have to define them.
NOTE: I had an issue with parameter arrays. The datatype has to match the varchar2 length. I have some if statements that look for specicically named parameters and hard code the type and length. This would have to be removed or modified for your code.
The code executes the plsql block and writes it out to the page.
If an error occures, I show the error page which populates an error message String. In the main exception blcok, if the error message was set, saves the error.
NOTE: saveError() is specific to my code. You will need to remove this or modify ot to write the error for how you store it.
The finally block closes the statement and connection

showFile:
This is only used for a file download.
NOTE: my code uses the images table for files. If yours is different or defined by Dad, you will need to change the code accordingly.

Dad class:
I've created a very simplified version of Dads. it simply has db (in my case prod or test), schemaName, minConnections and maxConnections.
You may need to extend the Dad class and make appropriate chjanges in the code.
I also do not store and manage Dads in a config file. I just hard code then in the ConnectionPool.getPoolDataSource. ConnectionPool has a collection of dads. If dads is not initialized, I initialize and add multiple Dads.
