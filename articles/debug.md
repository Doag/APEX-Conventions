# Debug
## General
Errors in APEX are often represented by an info field or an Ajax message in the application.   
Sometimes this error message does not help to find the source.  
For that we use the given tools like browser or APEX debug.

* APEX Debug
* Chrome/Safari/Mozilla Dev tools

## Using APEX Debugging
To use the APEX debug, it must be activated
![Debug On](https://github.com/THDevelop/Home/blob/master/TurnDebugOn.png)

## Best Practices
1. Open Dev tools from browser (right click on the page and inspect)

2. Select Console in Dev tools

3. Turn on the debug mode

4. Page reloaded   
   (Now you can see in the console executed code which is executed when loading a page. This is only  
    displayed if the DEBUG mode has been activated before.   
    The first errors could be analyzed here, e. g. a Javascript that fails during the page load.)
   ![Browser Console](https://github.com/THDevelop/Home/blob/master/BrowserConsole.png)

5. If the error is not visible in the console, use View Debug  
  (An advantage of the VIEW DEBUG is not only finding errors but also analyzing the performance. You 
   can see exactly where the page needs the longest.  
   This can be looked at more closely if you notice performance problems on a page.   
   INFO To understand the rendering of APEX it helps to look at View Debug too.)
  ![View Debug](https://github.com/THDevelop/Home/blob/master/ViewDebug.png)
  ![View Debugdetails](https://github.com/THDevelop/Home/blob/master/ViewDebugDetails.png)


With APEX 5.1 and the introduction of the Interactive Grid, more and more Javascript will be used in applications.
In order to find errors quickly, it is advisable to have a look at https://apex.mt-ag.com/jslgr.
