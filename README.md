# mod-plsql
A java servlet to replace deprecated apache mod_plsql cartridge

Created by Ray DeBruyn
ray@eis.ca
Contact me:
- if you wish to help extending the functioanlity.
- if you would like help adapting the code to meet you specific needs. I can be hired to do so.

I wasn't interested in building a full ords.jar functionality. We have used pl/sql web toolkit for 20 years or so. We were on a path to upgrade our servers and couldn't install the older version of mod_plsql. Oracle had deprecated it in the 11g release. I needed a solution to be able to continue using the existing code.

There are a few solutions out there, but any one required modification to run my code. Many were overly complicated for my taste. I prefer a simple approach and highly readable code.

One big issue I faced was in plsql index by table bind variables. I evolved a pattern to write my plsql packages. I should have declared any plsql index by table variable to be of the same type such as owa.vc_arr. Instead I declared a local type in each package always named the same. names_arr is a table of varchar2(30) and values_arr is a table of varchar2(4000). You have to know the varchar2 length in order to bind the plsql index by table, so I query the user_arguments data dictionary table. That info allows me to decide if I'm binding a string or a plsql index by table as well as the varchar length.

Dad configuration will need work. Right now, the dad is configured in the code. I would like to use a conf file for all dads or a conf file for each dad. I would also like to extend the application to manage the dads.

I always used a table called images for file uploads, so my code does not include that as far as the dad configuration. I also always assume a path of docs for file fetches

I've implemented connection pooling, but the dad configuration is not controlling the initialization of the pooldatasource. I set them all up the same for min/max connections etc.

I'm working on file upload where the parameters are sent as an array of names and an array of values. The issue here is the uploaded value includes the filename and the blob. After insert into the images table, I need to modify the filename since it's the primary key and must be unique. You can't change the values in the request object. 

 The normal way to access parameters is by name. For each name you get the array of values. Then, if I get a multipart file upload value, I can't easily tell which value is the array needs to change. I think I have a solution in place, but have to wait to test.
