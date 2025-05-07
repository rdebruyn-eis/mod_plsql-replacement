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
