PointerEvents & PointerEventWrapper
===================================
What is it?
-----------
**PointerEvents:** (not to be confused with PointerEvent) is an API for handling & binding a PointerEvent, 
MouseEvent, orTouchEvent in a consist cross browser compatable way. 

**PointerEventWrapper:** A wrapper arround these events so that old and new code can easily interact with 
them as if they were all the same. This makes adding support for the PointerEvent effortless. Secondly, we 
add some convenience properties and methods to make these events easier to work with.

Just incase you were wondering, it's only 4.4kb minified.

Wait, whats a PointerEvent?
---------------------------
**PointerEvent:** A new event specificification for describing how input devices such as a mouse, pen, 
or a touchscreen interact with the DOM.  Currently it's only supported by IE 9+, but most major browsers 
plan to impliment it in the near future. You can see the full specification 
[here](https://www.w3.org/TR/pointerevents/).

**pointerType or pointer:** The hardware device initiating the PointerEvent. Currently the pointerType 
can be a touch, mouse, or pen.

**action:** The action initiating the PointerEvent, such as "down" in "pointerdown".  The other possible 
actions are "up", "move", "over", "out", "cancel", "enter", & "leave". 

Why use PointerEventWrapper?
----------------------------
The api is easy to use, compatible with all browsers, noramlizes these events, and will save you time is 
many ways. Here are few example of to use the PointerEvens api.

Examples:
--------
##### Binding Events:
Here we will bind all events associated with the move and down action:

     // With PointerEvents API:
     PointerEvents.bind(document, 'move down', handler)

     // The above is equivalent to this jQuery:
     $(document).on("touchmove mousemove touchstart mousedown", handler)

     // and equivalent to this vanilla js:
     docuement.addEventListener('mousemove', movehandler)
     docuement.addEventListener('mousedown', downhandler)
     docuement.addEventListener('touchmove', movehandler)
     docuement.addEventListener('touchstart', downhandler)

     // NOTE: Any form of event binding will work with PointerEvents.
     // PointerEvents.bind() is merely a convenience.
     
##### Handling Events:
Points can be a touch, mouse, or pen. The PointerEvents API allows you to treat them all the same, no more writing code to handle differnet events. Here we'll use `point` exactly how you would have used `MouseEvent`, `TouchEvent`, or `PointerEvent`.
     
     function handler(e){
         // Wrap the event with PointerEventWrapper
         var pe = PointerEvents.parse(e)
         var point = pe.changedPoints[0]

         // Handle move events
         if(pe.move){
             // We track all previous points for you
             // and provide some useful properties.
             var clientDeltaX = point.deltaX

             // You can still access the old point properties too.
             var pointOld = pe.points[point.pointerId]
             var screenDeltaX = pointOld.screenX - point.screenX

             // Catch all touch events
             if (pe.isTouch)
                 console.log('This event was generated by a touch')

             // Catch mouse events on browsers with native PointerEvent
             else if (pe.isMouse)
                 console.log('This event was generated by a mouse')

             // Catch pen events on browsers with native PointerEvent
             else if (pe.isPen)
                 console.log('This event was generated by a pen')

             // Browsers without native PointerEvent will fire fake mouse
             // events to accompany touch events.  You will have to test
             // for this on your own.  Most commonly you will just test
             // for isTouch.
             else
                 console.log('This could be a real mouse or pen event, a fake
                 mouse or pen event, but never a touch!')
         }

         // Handle down events
         if(pe.down){
             if (pe.isTouch)
                 console.log('Touch down!")
             else
                 console.log('Mouse or pen down!")
         }
     }
     
##### The benefits of PointerEventWrapper:
Here we'll calculate the client delta for mouse and touch events with and without the PointerEventWrapper.

     // With PointerEventWrapper
     function handler(e){
         var pe = PointerEvents.parse(e)
         var point = pe.changedPoints[0]
         if(pe.move){
             var clientDeltaX = point.deltaX
             var clientDeltaY = point.deltaY
         }
     }

     // Without requires 75% more code.
     var prevY, prevX
     function handler(e){
         var clientDeltaX, clientDeltaY, touch
         if(e.type == 'mousedown'){
             prevX = e.clientX
             prevY = e.clientY
         }
         else if(e.type == 'touchstart'){
             touch = e.changedTouches[0]
             prevX = touch.clientX
             prevY = touch.clientY
         }
         else if(e.type == 'mousemove'){
             clientDeltaX = prevX - e.clientX
             clientDeltaY = prevY - e.clientY
         }
         else if(e.type == 'touchmove'){
             touch = e.changedTouches[0]
             clientDeltaX = prevX - point.clientX
             clientDeltaY = prevY - point.clientY
         }
         else if(e.type == 'mouseup' || e.type == 'touchend'){
             prevX = undefined
             prevY = undefined
         }
     }
     
##### Instantly make old code compatible with a PointerEvent.
An existingHandler for TouchEvents and or MouseEvents is shown below.  Adding one line
of code is all you need.

     function existingHandler(e){
         e = PointerEvents.parse(e)
         // Any existing code here will instantly work with PointerEvents,
         // even if it was not originally designed to do so, without any
         // changes! However, a quick refactor cloud reduce your code base
         // significantly.
     }
	 
##### Some shortcuts that can be uefull with other libraries:
Calling toString on a PointerEvents action will return a space separated string of the required events.

     var downString = PointerEvents.down.toString()
     > "pointerdown mousedown touchstart"

     // Bind down actions with jQuery
     $(document).on(PointerEvents.down.toString(), downHandler)

     // Bind multiple down actions with jQuery
     $(document).on(PointerEvents.down + ' ' + PointerEvents.up, downUpHandler)