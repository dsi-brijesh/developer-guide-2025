-------------------------------------------------------------------------------------------------------------------------------------------

context.watch<type>() 
- should be called inside build method.
- it is used to listen to any changes in provider or change notifier.
- when notifyListeners() is called, the widgets that using context.watch get's rebuild.

context.read<type>()
- can be called when just triggering an action.
- the widget where you call it doesn't need to rebuild, but others might be listening to the context.watch.

-------------------------------------------------------------------------------------------------------------------------------------------

🔁 What happens:

In This Widget	What Happens?

You call read().clearHistory()	------------>    🔄 Calls notifyListeners()
This widget uses read() -------------------->	   ❌ Doesn’t rebuild
Another widget uses watch() or Consumer	---->    ✅ Rebuilds automatically

-------------------------------------------------------------------------------------------------------------------------------------------

✅ Summary

Use Case	                              Use read()	         Rebuilds this widget?	   Rebuilds others?
Call method that updates state ------>    ✅ Yes	                 ❌ No	                ✅ Yes
Read a changing value to display ---->	  ❌ Use watch()	         ✅ Yes	                   —

-------------------------------------------------------------------------------------------------------------------------------------------

🧠 Provider V/s Get_It

Use Case	                                     Use context.watch()	     Use serviceLocator
Rebuild UI on theme/font change	----------->         ✅ Yes	                    ❌ No
Read once (e.g. in initState)	  ----------->         ✅ Yes (or read())	      ✅ Yes
Background service / logic	    ----------->         ❌ No	                    ✅ Yes
Avoid rebuild for performance	  ----------->         ❌ No	                    ✅ Yes

-------------------------------------------------------------------------------------------------------------------------------------------

🧠 Tip: Difference between read, watch, and select

Method	                               Rebuilds on notifyListeners()?	            		              Best For

context.watch<T>()     -----------> 	             ✅ Yes					                          Reading reactive values
context.read<T>()     ------------> 	             ❌ No					                          Calling methods or setting values
context.select<T, R>() -----------> 	             ✅ Yes (but only for selected field)	  Optimized listening to a single field

-------------------------------------------------------------------------------------------------------------------------------------------
