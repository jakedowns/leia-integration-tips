# Example Leia Integration

Here is a commit for basic backlight mode switching being added to the MPVActivity class of the MPV w/ Leia fork:
[https://github.com/jakedowns/mpv-android/commit/8af89fb04339fd24348d9ed3d4bb0f07de29d653](https://github.com/jakedowns/mpv-android/commit/8af89fb04339fd24348d9ed3d4bb0f07de29d653)

The basics are:
- add leia sdk to `buid.gradle` (add maven repo as well)
- import libraries into the activity where the 3D effect is desired
- extend the activity with `BacklightModeListener`
- add some new tracking variables at the class level: 
```
    // LitByLeia
    private var mRenderModeIsLeia3d = false
    private var mPrevDesiredBacklightModeState = false
    private val mExpectedBacklightMode: BacklightMode? = null
    private var mBacklightHasShutDown = false
    private val mIsDeviceCurrentlyInPortraitMode = false
    private var mDisplayManager: LeiaDisplayManager? = null
    private var mLeiaQuery: SimpleDisplayQuery? = null
```    
- in `onCreate`, `registerBacklightModeListener`
- add required `onBacklightModeChanged` method
- add `Enable3D`, `Disable3D`, `SetBacklightMode`, `HasBacklightShutdown`, `checkShouldToggle3D` methods
- call `Disable3D` in handlers such as `onDestroy` and any other events where you want the backlight to switch back to 2D mode

That's the basics of being able to toggle between 2D/3D mode

Beyond that, you need to figure out where in your app's rendering stack is the appropriate time to hook in the special interlaced shader for converting 2 or more views into a single interlaced image.

For example, in MPV, the app already has a pipline for compiling / executing shaders, so I just include the shader file in the app's resources and load it in by setting it as the desired user-preferred shader when activating the mode:

Example shaders for 2x1 (SBS, L,R), 2x2 (SBS FL,L,R,FR), OverUnder (L/R) can be found here:
[https://github.com/jakedowns/mpv-android/tree/jakedowns/LitByLeia/app/src/main/assets](https://github.com/jakedowns/mpv-android/tree/jakedowns/LitByLeia/app/src/main/assets)
