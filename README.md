# Android-InterView
android 面试汇总
##如果后台的Activity由于某原因被系统回收了，如何在被系统回收之前保存当前状态？
###在onSaveInstanceState()中保存其状态。activity被置于后台时，在执行onStop之前，通过onSaveInstacneState
###保存当前activity状态。如果被系统杀死，再次被调起时，，在onResume之前，onStart之后，通过
###onRestoreInstanceState（）来恢复其状态。如果没有被系统杀死，则不执行onRestoreInstanceState。
###如果用户点击back键，则onSaveInstanceState()不会被调用。onSaveInstanceState()在以下几种情况会被调用：
###1，按home键时；2，横竖屏切换时；3，按电源键时；4，从当前activity进入另一个activity时。
###系统默认只会保存ui状态--view hierarchy，比如editext里面的text，或者listview的scroll位置，
###不会保存别的信息，如果需要保存则复写此方法。
![mahua]( https://github.com/paceboy/Android-InterView/blob/master/handler-howtowork.png)
