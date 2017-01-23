1. From [here](https://github.com/mpcjanssen/simpletask-android/issues/507): _Using saved filters in widgets. Widget portability across devices._  
> For now easiest way to achieve this is to select the filter in the app and then create the widget. it will pre fill with the current filter.  

 You can define a lua script as well as custom sorting and filters and then save the filter to the right drawer. Thus, if you wish to exclude certain lists or tags, for example, you can do that without using Lua. Once you have all predefined filters stored this way, in order to create a widget with a particular filter, just select that filter in the main app. Then, click on the home screen (do not close the filter (with back button if the option is enabled) or hitting `X`) and create a new Simpletask widget. The last opened filter will be used automatically.  
 
 In order to create the same widgets across devices, export the filters. They will be saved as saved_filters in the data folder. Similarly, export the Lua configuration. Copy both to new device, import them both, and use the above method to create the same widgets.

2. From [here](https://github.com/mpcjanssen/simpletask-android/issues/494). _Editing the todo.txt file from within the Simpletask app._  
> This is almost possible with File Order sort -> select all -> edit. You can successfully re-order lines (although it is difficult to add new lines in the middle); The only thing that's missing is white space.  

 Edit: It is not difficult to add lines in the middle. If there is any problem, try the [Hacker's keyboard](https://play.google.com/store/apps/details?id=org.pocketworkstation.pckeyboard&hl=en).
