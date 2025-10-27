<a name="top"></a>

[<-- back](../Content.md)


# Swift Notes - UIView Layout & Appearance

### Layout → appearance 

- `clipsToBounds = true` - Clips subviews to the view’s bounds; any content extending beyond the bounds is not visible.

### Geometry
- frame → Position & size in the superview’s coordinate system.
- bounds → Position and size in the view’s own coordinate system (usually (0,0)).

### Initialization

- `init(frame:)` - Called when creating the view programmatically.
- `init(coder:)` - Called when the view is loaded from a nib or storyboard.
- `awakeFromNib()` - Called after the view has been fully loaded from a nib/storyboard (outlets are connected).

### View Hierarchy Lifecycle

* view
    - `willMove(toSuperview newSuperview: UIView?)` - Called before the view is added to or removed from a superview (newSuperview is nil when being removed).
    - `didMoveToSuperview()` - Called after the view is added to or removed from its Superview.

* window
    - `willMove(toWindow:)` - Called before the view is added to or removed from a window (newWindow is nil when being removed).
    - `didMoveToWindow()` - Called after the view is added to or removed from a window



### Drawing Methods

- `draw()` Handles appearance (rendering)
    *  Used to perform the custom rendering, Called when the view needs to redraw its contents — e.g., color, shapes, gradients
    * It gets triggered by setNeedsDisplay() or the first time the view is displayed.
    * It affects pixels, not geometry(or layout).
- `setNeedsDisplay()` - Calling setNeedsDisplay() tells the system to call draw(_:) during the next drawing cycle(asynchronously).

### CALayer
Every UIView has an associated CALayer (view.layer) responsible for:
- Rendering content to the screen,
- Managing animations, shadows, and corner radius,
- Handling visual properties (e.g., borderColor, shadowOpacity, masksToBounds).

### View Properties
- isOpaque = true → Optimizes rendering by assuming the view fully covers its bounds.
- removeFromSuperview() → Detaches the view from its superview.
- isHidden → Hides or shows the view.
- isUserInteractionEnabled → Enables or disables touch interactions.

### Animation & Layout

- `UIView.animate(withDuration:animations:)`: Performs animations on layout or appearance changes.
- `sizeThatFits(_:)`: Returns the optimal size for the view’s content (used for manual layout)

### Bounds & Clipping

- `layer.masksToBounds`: Clips sublayer content to the layer’s bounds.
- `view.clipsToBounds`: If set to true, any part of subviews that extends outside the view’s bounds gets clipped (not visible).

### Coordinate System Conversion

- `convert(_ point: CGPoint, to view: UIView?)` → Converts a point from the current view’s coordinate system to another view’s coordinate system..
- `convert(_ point: CGPoint, from view: UIView?)` → Converts a point from another view’s coordinate system to the current view’s coordinate system..
- Similarly works for CGRect.



## Layout Methods

`layoutSubviews()` - 
* Handles geometry and layout of the view’s subviews.

* Called automatically when:
    - The view’s bounds change,
    - Constraints change, or
    - A subview’s layout changes.  

* You override this method to manually adjust subviews’ frames or positions.  

* layoutSubviews() only runs when a layout update is actually needed —
either because something changed or because the view was explicitly marked as needing layout.

* Never call it directly, UIKit manages this lifecycle — instead use:
    - `setNeedsLayout()` → Marks the view as needing a layout update.  
        * It doesn’t immediately call layoutSubviews().

        * Instead, UIKit schedules it for the next run loop cycle (asynchronous update).
    - `layoutIfNeeded()` → Forces the layout immediately, but only if a layout pass is already pending.
        * This runs synchronously in the current run loop.
        * Commonly used when you need the view’s layout to be up-to-date before performing animations or measuring sizes


Q. When you modify the frame of a view, under what circumstances will Auto Layout constraints override your changes, and how can you correctly update the view’s position or size while using Auto Layout?

If Auto Layout is active (i.e., the view has constraints), manually setting frame does not persist because Auto Layout recomputes the frame from constraints on the next layout pass.  

Options to update view
1. Update constraints directly
```
myViewWidthConstraint.constant = 120
view.layoutIfNeeded()
```

2. Use Auto Layout–safe methods like layoutIfNeeded() or animations
```
UIView.animate(withDuration: 0.3) {
    myViewWidthConstraint.constant = 150
    self.view.layoutIfNeeded()
}
```

3. If you truly need manual layout:  
    Disable Auto Layout for that view:
```
myView.translatesAutoresizingMaskIntoConstraints = true
```

If Auto Layout is on → constraints win.
If Auto Layout is off → frame wins.



## Touch Handling

- hitTest(_:with:) → Determines which view should receive a touch event. UIKit traverses subviews recursively to find the topmost eligible view where:
    * isUserInteractionEnabled = true,
	* isHidden = false,
	* alpha > 0.01.

- point(inside:with:) → Checks if a touch point lies inside the view’s bounds.

- ex:
```
ParentView (userInteractionEnabled = true)
 ├── SubviewA (userInteractionEnabled = true)
 └── UIButton (covers SubviewA visually)
```
UIKit starts hit-testing from the window and works downward through the hierarchy:  
1. UIWindow.hitTest(_:with:)
→ Called first, checks if the touch is inside the window.
✅ Yes → forwards to window’s root view controller’s view (ParentView).

2. ParentView.hitTest(_:with:)  
→ Calls point(inside:with:) → returns true (touch is within ParentView’s bounds).  
→ Iterates subviews in reverse order (front to back — last added is tested first).  
    - So it first checks UIButton.
	- Then SubviewA (only if UIButton didn’t claim the touch).

call stack -  
```
UIWindow.hitTest(point, event)
 └── ParentView.hitTest(point, event)
      ├── UIButton.hitTest(point, event)
      │     └── UIButton.point(inside:with:) → true ✅
      │     → UIButton returned
      └── SubviewA.hitTest(...)  // never called
```



[⬆️ Back to Top](#top)



