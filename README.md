# Swift Notes - UIView Layout & Appearance

### Layout → appearance 

- `clipsToBounds = true` - affects the view's subviews — anything that extends outside the view's bounds will be cut off.

### Geometry
- frame → position & size in superview’s coordinates.
- bounds → internal coordinate system (usually (0,0)).

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



# iOS architecture



iOS follows a layered architecture, where each layer builds on the services of the one below it. 
Layer hierarchy:  Cocoa Touch(top level) → Media → Core Services → Core OS(bottom level)


### 1️⃣ Core OS Layer (Bottommost Layer)
This is the foundation of iOS where low-level components and system-level services reside.

Key Components:
- Kernel (XNU Hybrid Kernel): 
    Combines a mach microkernel(used to do scheduling, IPC and memory management) and the FreeBSD kernel(manages networking, file system and security services).
- Device Drivers: 
    Handle communication with hardware components such as the camera, microphone, and sensors.
- Security: Implements app sandboxing, manages trusted execution, encryption, and secure boot
- LibSystem: A C-based library providing fundamental system services like threading, networking, and file handling.


### 2️⃣ Core Services Layer
Provides system-level services and APIs that apps rely on

Key Components:
- Core Foundation Services: Low-level data management, preferences, and utility APIs.
- Databases: Includes Core Data and SqLite support.
- Networking Services: Uses CFNetwork high level networking api.
- File System Access: Provides structured file handling APIs.
- Concurrency: GCD (Grand Central Dispatch) and NSOperationQueue built on this layer.


### 3️⃣ Media Layer
Responsible for graphics, animation, audio, and video—everything that creates the visual and rich multimedia experience.
Key Components:
- Graphics:
	*	Core Graphics (Quartz 2D): 2D drawing and rendering. - used to draw line, quadriterals etc
	*	Metal: High-performance graphics and GPU-based rendering (used in games and 3D apps).
	*	Core Image: Used to alter the image

- Animation:
	*	Core Animation: Used to animate UI elements efficiently across multiple layers.


- Audio & Audio:
	*	Core Audio, AVFoundation for Playback, recording, and processing of audio.
	*	AVFoundation for Video capture, playback, and editing.

### 4️⃣ Cocoa Touch Layer (Topmost Layer)
The application layer, where developers interact with iOS APIs to build apps
Key Components:


-	UI Frameworks:
	    *	UIKit: imperative UI framework.
	    *	SwiftUI: declarative UI framework.


-	Event Handling: Manages touch, gestures, and other user interactions.


-	View Controllers: Manage screen-level UI logic and the app’s view hierarchy.
-	Auto Layout: Handles responsive and adaptive layout for different screen sizes.
-	Other Services: Notifications, multitasking, and accessibility APIs.



NOTE:
```
Kernel / FreeBSD Sockets 
   ↓
LibSystem (C Networking APIs)
   ↓
CFNetwork (Foundation-level networking)
   ↓
URL Loading System (NSURLSession, Swift APIs)
```
