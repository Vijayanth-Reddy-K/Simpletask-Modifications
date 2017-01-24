1. _Using saved filters in widgets. Creating filter shortcuts. Widget portability across devices._  
 
 From [here](https://github.com/mpcjanssen/simpletask-android/issues/507): 

 > For now easiest way to achieve this is to select the filter in the app and then create the widget. it will pre fill with the current filter.  

 You can define a lua script as well as custom sorting and filters and then save the filter to the right drawer. All the app filters, options, sort order, and entries in the script window will be stored. Thus, if you wish to exclude certain lists or tags, for example, you can do that without using Lua. Once you have all predefined filters stored this way, in order to create a widget with a particular filter, just select that filter in the main app. Then, click on the home screen (do not close the filter (with back button if the option is enabled) or hitting `X`) and create a new Simpletask widget. The last opened filter will be used automatically.  
 
 The saved filters can also be accessed in one more way apart from the right drawer. Open the right drawer and press and hold the filter of choice. Click on `To homescreen` to create a 1×1 widget on the homescreen that directly opens the saved filter. (Just for completeness: Clicking on `Update` saves the current filter settings to the selected saved filter.  
 
 In order to create the same widgets across devices, export the filters. They will be saved as `saved_filters.txt` in the data folder. Similarly, export the Lua configuration. Copy both to new device, import them both, and use the above method to create the same widgets.

2. _Editing the todo.txt file from within the Simpletask app._  

 From [here](https://github.com/mpcjanssen/simpletask-android/issues/494). 
 > This is almost possible with File Order sort -> select all -> edit. You can successfully re-order lines (although it is difficult to add new lines in the middle); The only thing that's missing is white space.  

 Edit: It is not difficult to add lines in the middle. If there is any problem, try the [Hacker's keyboard](https://play.google.com/store/apps/details?id=org.pocketworkstation.pckeyboard&hl=en).

3. _Understanding recurring and hidden tasks. The complete ramifications of using_ `rec:` _and_ `h:1`_._

 First, read the descriptions provided [here](https://github.com/mpcjanssen/simpletask-android/blob/master/src/main/assets/index.en.md#extensions).  

 - **Recurring Tasks**: When you set up a recurring task, once it is completed, the _existing task_ is marked as done and a _new task_ is created. The lists, tags, priority and task description in the new task are same as the one in the old task. The due and threshold dates and creation date (if any) change however. How they change depends on the kind of recurrence behaviour. Below, we explore the difference between `rec:+1d` (calculate from original dates) and `rec:1d`(calculate from completion date).   
   
   For the sake of simplicity, creation date is not included, though it also changes to the date the new task is created. Also note that the actual due and threshold dates are not included as dates. Instead, "1 day ago", "today", "tomorrow", etc. are used for ease of understanding.

    _Test task:_ `Test_rec rec:+1d due: 1 day ago t:3 days ago`  

    _Mark as done_: `Test_rec rec:+1d due: today t:2 days ago`  
    _Mark as done again_: `Test_rec rec:+1d due: tomorrow t:1 day ago`  
    _Mark as done again_: `Test_rec rec:+1d due: day after tomorow t:today`  
    ... _and  so on_ ...

    _Test task:_ `Test_rec rec:1d due: 1 day ago t:3 days ago`  

    _Mark as done_: `Test_rec rec:+1d due: tomorrow t:tomorrow`  
    _Mark as done again_: `Test_rec rec:+1d due: tomorrow t:tomorrow`   
    _Mark as done again_: `Test_rec rec:+1d due: tomorrow t:tomorrow`  
    ... _and  so on_ ...  
				
	   That is, in the second case, because the completion date is the same in the second and third iterations, the due and threshold dates calculated are the same, and hence the new task is constant after the first iteration until the xompletion date changes. Also note that unlike the first case, the threshold and due dates in the new task end up being the same as they are calculated from the same (completion) date.
			
 - **Hidden Tasks**: Tasks which have `h:1` mentioned are not displayed in Simpletask unless the `Show hidden tasks` option is selected from the `OTHER` tab. This lets the user define those lists and tags that are to remain persistent, _viz._ available in the left drawer and the list and tags lists even if no other task which has them exists. So, something like:
	
	  `h:1 Persistent Lists: @A @B @C` and `h:1 Persistent Tags: +Z +Y +X` can be included.
 
4. _Due Date, Threshold Date and Create is Threshold._

 Format for due and threshold dates provided [here](https://github.com/mpcjanssen/simpletask-android/blob/master/src/main/assets/index.en.md#extensions).  

 Due date defines when the task is due. Threshold date defines when the task is supposed to begin. The date of creation of the task is not necessarily the date the task needs to begin.  
	
	Unchecking the option in the `OTHER` tab to show tasks with threshold dates in the future allows you to forget about tasks you do not wish to be reminded of currently. Thus, if you have a task that is due in a month, but you only want to get to it after 2 weeks, set the appropriate threshold and due dates and unchek the above mentioned setting. You will see the task in the task list only after 2 weeks. In addition, Simpletask lets you be reminded when the Threshold date occurs as well.  

 `Create is Threshold` setting from the `OTHER` tab implicitly takes the creation date to be the threshold date if threshold date is missing. However, this has no effect on filtering. Only on sorting. And when this setting is active, the option to show tasks with threshold dates in the future also does not hide tasks if unchecked.

