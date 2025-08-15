# Swift Notes - UIView Layout & Appearance

### Layout → appearance 

- `clipsToBounds = true` - affects the view's subviews — anything that extends outside the view's bounds will be cut off.

### Initialization

- `init(frame:)` - gets called when you are making view programmatically
- `init(coder:)` - gets called when you are loading view from the nib or storyboard
- `awakeFromNib()` - gets called after the view had been loaded from the nib or storyboard

### View Hierarchy Lifecycle

- `willMove(toSuperview newSuperview: UIView?)` - Called before the view is added to (newSuperview → reference to new view) or removed from a UIWindow(newSuperview → nil)
- `didMoveToSuperview()` - Called after the view is added to or removed from a Superview
- `willMove(toWindow:)` - Called before the view is added to (toWindow → reference to new window) or removed from a UIWindow(toWindow → nil)
- `didMoveToWindow()` - Called after the view is added to (self.window → reference to new window) or removed from a UIWindow(self.window → nil)

### Layout Methods

- `layoutSubviews()` - Called whenever a view's bounds, constraint's or its subview's changes. You never call layoutSubviews() directly — you call setNeedsLayout() (asynchronous) or layoutIfNeeded() (synchronous) to trigger it. Its generally used to adjust the layout of view subviews. It affects geometry. Generally used to do manual frame-based layout
- `setNeedsLayout()` and `layoutIfNeeded()` - Used to trigger layoutSubviews, setNeedsLayout() runs in next run loop cycle (asynchronous), whereas layoutIfNeeded() runs immediately if current layout pass is pending(synchronous)

### Drawing Methods

- `draw()` - Used to perform the custom rendering, It's called when setNeedsDisplay() or setNeedsDisplay(_:) is invoked, or when the view first appears and needs to render. It affects pixels.
- `setNeedsDisplay()` - Calling setNeedsDisplay() tells the system to call draw(_:) during the next drawing cycle(asynchronously).

### CALayer

- Every uiview have the layer property, that helps it to render contents on screen, handle animation and various properties

### Touch Handling

- `hitTest()` - Used to determine which view should receive a touch event, UIKit recurcively calls the subviews and finds the most specific view that contains the touch point and (isUserInteractionEnabled = true and isHidden = false, alpha > 0.01 for that view)
- `point(inside:with:)`

### View Properties

- `isOpaque = true`
- `removeFromSuperview()`
- `isHidden = true`
- `isUserInteractionEnabled` - determines whether a view can receive touch events.

### Animation & Layout

- `UIView.animate`
- `sizeThatFits`

### Bounds & Clipping

- `view.layer.masksToBounds`
- `view.clipsToBounds`

### Coordinate System Conversion

- `convert(_ point: CGPoint, to view: UIView?)` → converts a point from the current view's coordinate system to another view's coordinate system.
- `convert(_ point: CGPoint, from view: UIView?)` → converts a point from another view's coordinate system to the current view's coordinate system.
- Similarly works for CGRect.
