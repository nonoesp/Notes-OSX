# Notes-OSX
Useful notes of Cocoa in OSX (Xcode, Objective-C, Swift, and OpenGL).

## Table of Contents

* Reference
* Syntax
* OpenGL
* To-dos
* Math

## Reference

* [Create a Singleton Class](http://www.galloway.me.uk/tutorials/singleton-classes/)

## Syntax


### NSLog

Logging on the console.

Objective-C
```objc
// NSLog an NSString
NSLog(@"Hello, World!");

// NSLog a formatted NSString
NSLog(@"My name is %@", @"Steve");

// NSLog formatted NSString depending on a boolean value
NSLog(@"%@", isActive ? @"YES" : @"NO");
```

## OpenGL

Even though these notes are not C#, I will keep them here until this section grows enough to create a single repository of OpenGL notes.

### OpenGL References

* [Introduction to GLKit](https://developer.apple.com/library/ios/documentation/GLkit/Reference/GLKit_Collection/index.html#//apple_ref/doc/uid/TP40010915)
* [Drawing with OpenGL and GLKit](https://developer.apple.com/library/ios/documentation/3DDrawing/Conceptual/OpenGLES_ProgrammingGuide/DrawingWithOpenGLES/DrawingWithOpenGLES.html)
* [CocoaGL Tutorial](https://github.com/beelsebob/Cocoa-GL-Tutorial)
* [Build A Windows.Forms+GLControl Based App](http://www.opentk.com/doc/chapter/2/glcontrol)
* [Understanding Depth and Stencil Buffers](https://open.gl/depthstencils)
* [Object Outlining](http://www.flipcode.com/archives/Object_Outlining.shtml) (2002)

### Subclassing NSOpenGLView

Create a new OS X Cocoa Project with a single View Controller. In the NIB file, drag an NSOpenGLView to your View Controller.

Now, go and add a new Cocoa class to your project, which should be subclassing NSOpenGLView. Call it something like MyNSOpenGLView, CustomNSOpenGLView, or even GraphicsView—you will need to change the class of the NSOpenGLView inside your NIB file to the class name you selected for your NSOpenGLView subclass. We will stick with CustomNSOpenGLView for now.

In the CustomNSOpenGLView.m file, of your class, import the following:

```objc
#import "CustomNSOpenGLView.h"
#import <OpenGL/gl.h> // import OpenGL

@implementation CustomNSOpenGLView
```

Everything should be working properly. Now, we just need to add some setup and drawing code in our class’ `drawRect:` method (which we need to add). *This code is from Apple’s [reference](https://developer.apple.com/library/mac/documentation/GraphicsImaging/Conceptual/OpenGL-MacProgGuide/opengl_drawing/opengl_drawing.html).*

```objc
-(void) drawRect: (NSRect) bounds
{
    glClearColor(0, 0, 0, 0);
    glClear(GL_COLOR_BUFFER_BIT);
    drawAnObject();
    glFlush();
}

// Draw a triangle
static void drawAnObject ()
{
    glColor3f(1.0f, 0.85f, 0.35f);
    glBegin(GL_TRIANGLES);
    {
        glVertex3f(  0.0,  0.6, 0.0);
        glVertex3f( -0.2, -0.3, 0.0);
        glVertex3f(  0.2, -0.3 ,0.0);
    }
    glEnd();
}
```

### Applying Transformation Matrices

There are two different approaches I have managed to apply transformations in order to modify how objects are displayed:

* Multiply a 4x4 matrix (GLKMatrix4) by a column vector (GLKVector3) to obtain the transformed point.
* Multiplying a 4x4 matrix (GLKMatrix4) to the current glMatrixMode(GL_MODELVIEW) after loading the identity matrix.

#### Multiply GLKMatrix4 by a GLKVector3

```objc
// Define a sample vector to draw a point
GLKVector3 point = GLKVector3Make(0.0f, 0.0f, 0.5f);
// Create a transformation matrix (a translation, for instance)
GLKMatrix4 translate = GLKMatrix4MakeTranslate(1.0f, 0.5f, 0.0f);
// Multiply the transformation matrix by the column vector
GLKVector3 point_t = GLKMatrix4MultiplyAndProjectVector3(translate, point);
// Now our point_t has been translated by (1.0f, 0.5f, 0.0f)
// It's coordinates should now be (1.0f, 0.5f, 0.5f);
```

#### Multiply the Model View Matrix by a GLKMatrix4

```objc
// Create a transformation matrix (a translation, for instance)
GLKMatrix4 translate = GLKMatrix4MakeTranslate(1.0f, 0.5f, 0.0f);
// Set the matrix mode to model view
glMatrixMode(GL_MODELVIEW);
// Load the identity matrix
glLoadIdentity();
// Multiply the current matrix by our transformation
glMultMatrixf(translate.m);
```

#### Set An Isometric View

The following code works, but for some reason the Z axis seems to be scaled down. If you discover why, please let me know =)!

```objc
glMatrixMode(GL_PROJECTION);
glLoadIdentity();
glOrtho(0.0, 0.0, self.bounds.size.width, self.bounds.size.height, -100.0, 100.0);

glMatrixMode(GL_MODELVIEW);
glLoadIdentity();
glRotatef(-45.0, 1.0, 0.0, 0.0); // Rotate around Z
glRotatef(-45.0, 0.0, 0.0, 1.0); // Rotate around Y

// Draw here
```

## To-dos

* Creating a Protocol and A Delegate

## Math

### References

* [Constructing The LookAt() Matrix Directly](http://www.cs.virginia.edu/~gfx/Courses/1999/intro.fall99.html/lookat.html)
* [Perspective Projection](http://ogldev.org/www/tutorial12/tutorial12.html)

## License

Notes-OSX is licensed under the MIT license. (http://opensource.org/licenses/MIT)

## Me

I tweet at [@nonoesp](http://www.twitter.com/nonoesp) and write at [nono.ma/says](http://nono.ma/says). I would love to hear about it if you find these notes are useful. Thanks!
