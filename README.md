LongPressPopup
==============

<img src="sample.gif" title="sample" />

A library that let you implement a behaviour similar to the Instagram's <br />
long press to show detail one, with the option to put every kind of views inside it, <br />
(even web views, lists, pagers and so on) show tooltips on drag over <br />
and handle the release of the finger over Views <br />

[Changelog] (CHANGELOG.md)<br />


Download
------
####Gradle:
```groovy
compile @@@@@@@@@
```

##Usage

#####Basic
Here's a basic example <br />

```java
public class ActivityMain extends AppCompatActivity {
        
    @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            
            Button btn = (Button) findViewById(R.id.btn_popup);
            
            // Create a foo TextView
            TextView textView = new TextView(this);
            textView.setText("Hello, Foo!");
            
            LongPressPopup popup = new LongPressPopupBuilder(this)// A Context object for the builder constructor
                    .setTarget(btn)// The View which will open the popup if long pressed
                    .setPopupView(textView)// The View to show when long pressed 
                    .build();// This will give you a LongPressPopup object
                    
            // You can also chain it to the .build() mehod call above without declaring the "popup" variable before 
            popup.register();// From this moment, the touch events are registered and, if long pressed, will show the given view inside the popup, call unregister() to stop
        }
}
```

#####Advanced
Here are the functions you can use to customize the Popup and it's behaviour from the
LongPressPopupBuilder class: <br />

* ```public LongPressPopupBuilder setTarget(View target)``` <b>(null by default)</b>
⋅⋅⋅ Select which view will show the popup view if long pressed <br /> <br />
* ```public LongPressPopupBuilder setPopupView(View popupView)``` <b>(null by default)</b>
⋅⋅⋅ Select the view that will be shown inside the popup <br /> <br />
* ```public LongPressPopupBuilder setPopupView(@LayoutRes int popupViewRes, PopupInflaterListener inflaterListener)``` <b>(0, null by default)</b>
⋅⋅⋅ Select the view that will be shown inside the popup, and give the popup that will be
⋅⋅⋅ called when the view is inflated (not necessarily when shown, so not load images and so on in this callback,
⋅⋅⋅ just take the views like in the OnCreate method of an Activity) <br /> <br />
* ```public LongPressPopupBuilder setLongPressDuration(@IntRange(from = 1) int duration)``` <b>(500 by default)</b>
⋅⋅⋅ Pretty self explanatory right? **Captain here, the long press time needed to show the popup <br /> <br />
* ```public LongPressPopupBuilder setDismissOnLongPressStop(boolean dismissOnPressStop)``` <b>(true by default)</b>
⋅⋅⋅ Set if the popup will be dismissed when the user releases the finger (if released on a View inside
⋅⋅⋅ the popup, the View's or the given OnClickListener will be called) <br /> <br />
* ```public LongPressPopupBuilder setDismissOnTouchOutside(boolean dismissOnTouchOutside)``` <b>(true by default)</b>
⋅⋅⋅ If ```setDismissOnLongPressStop(boolean dismissOnPressStop)``` is set to false, you can choose to make
⋅⋅⋅ the popup dismiss or not if the user touch outside it with this boolean <br /> <br />
* ```public LongPressPopupBuilder setDismissOnBackPressed(boolean dismissOnBackPressed)```  <b>(true by default)</b>
⋅⋅⋅ If ```setDismissOnLongPressStop(boolean dismissOnPressStop)``` is set to false, you can choose to make
⋅⋅⋅ the popup dismiss or not if the user press the back button <br /> <br />
* ```public LongPressPopupBuilder setCancelTouchOnDragOutsideView(boolean cancelOnDragOutside)``` <b>(true by default)</b>
⋅⋅⋅ Set if the long press timer will stop or not if the user drag the finger outside the target View
⋅⋅⋅ (If the target View is inside a scrolling parent, when scrolling vertically the long press timer
⋅⋅⋅ will be automatically stopped <br /> <br />
* ```public LongPressPopupBuilder setLongPressReleaseListener(View.OnClickListener listener)``` <b>(null by default)</b>
⋅⋅⋅ This is a standard OnClickListener, which will be called if the user release the finger on a view inside
⋅⋅⋅ the popup, you can use this method or set a standard OnClickListener on the View you want, it will be called
⋅⋅⋅ automatically for you <br /> <br />
* ```public LongPressPopupBuilder setOnHoverListener(PopupOnHoverListener listener)``` <b>(null by default)</b>
⋅⋅⋅ This listener will be called every time the user keeps dragging it's finger inside or outside the popup
⋅⋅⋅ views, with a View reference and a boolean with the hover state <br /> <br />
* ```public LongPressPopupBuilder setPopupListener(PopupStateListener popupListener)``` <b>(null by default)</b>
⋅⋅⋅ This listener will be called when the popup is shown or dismissed, use this listener to load images or compile text views and so on <br /> <br />
* ```public LongPressPopupBuilder setTag(String tag)``` <b>(null by default)</b>
⋅⋅⋅ This method sets a tag on the LongPressPopup, the given tag will be returned in all the listeners. You can also set it in the build(String tag) 
⋅⋅⋅ method <br /> <br />
* ```public LongPressPopupBuilder setAnimationType(@LongPressPopup.AnimationType int animationType)``` <b>(none by default)</b>
⋅⋅⋅ This method set the opening and closing animation for the popup, can be none or from-to Bottom, Top, Right, Left, Center


<br /> <br />

Here's a complete example with all the options <br />
```java
public class ActivityMain extends AppCompatActivity {
     
    private TextView mTxtPopup;   
        
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        Button btn = (Button) findViewById(R.id.btn_popup);
        
        LongPressPopup popup = new LongPressPopupBuilder(this)
                        .setTarget(btn)
                        //.setPopupView(textView)// Not using this time
                        .setPopupView(R.layout.popup_layout, this)
                        .setLongPressDuration(750)
                        .setDismissOnLongPressStop(false)
                        .setDismissOnTouchOutside(false)
                        .setDismissOnBackPressed(false)
                        .setCancelTouchOnDragOutsideView(true)
                        .setLongPressReleaseListener(this)
                        .setOnHoverListener(this)
                        .setPopupListener(this)
                        .setTag("PopupFoo")
                        .setAnimationType(LongPressPopup.ANIMATION_TYPE_FROM_CENTER)
                        .build();
                
        // You can also chain it to the .build() mehod call above without declaring the "popup" variable before 
        popup.register();
    }
    
    @Override
    public void onViewInflated(@Nullable String popupTag, View root) {
        mTxtPopup = (TextView) root.findViewById(R.id.txt_popup);
    }
    
    @Override
    public void onClick(View view) {
        if(mTxtPopup != null && view.getId() == mTxtPopup.getId()) {
            Toast.makeText(Toast.makeText(ActivityMain.this, "TextView Clicked!"));
        }
    }
}
```