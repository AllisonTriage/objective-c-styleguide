<!-- 
Compiled by Allison Allain 
March 2014
-->

#iTriage Objective-C Style Guide

## Attributions
* [NYTimes Objective-C Style Guide] (https://github.com/NYTimes/objective-c-style-guide)
* [Apple] (https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [Google] (https://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)

## Overview

This guide is primarily based off of the [NYTimes Objective-C Style Guide] (https://github.com/NYTimes/objective-c-style-guide). Sections written by the New York Times are noted by (NYT) following the section header, or following the paragraph if an entire section isn't warranted. Several customization/additions have been made which are important to the iTriage team.  Suggestions for changes will be accepted in the form of pull requests.

The expectation is this: 

* All new functionality will adhere to these standards.  
* All code the iOS team touches going forward will be updated to adhere to these standards.

## Table of Contents

* [Xcode Project](#xcode-project)

* [Documentation] (#documentation)
    * [Comments] (#comments)
    * [Apple Doc] (#apple-doc)
    * [Deprecation] (#deprecation)

* [Formatting] (#formatting)
    * [Line And Character Spacing] (#line-and-character-spacing)
    * [Methods](#methods)

* [Classes] (#classes)
    * [Init and Dealloc](#init-and-dealloc) 
    * [Abstraction, Categories and Copied Code] (#abstraction-categories-and-copied-code)
    * [Multiple Classes in a File] (#multiple-classes-in-a-file)

* [Properties And Member Variables] (#properties--member-variables)
    * [Dot-Notation Syntax](#dot-notation-syntax-nyt)
    * [CGRect Functions](#cgrect-functions-nyt)
    * [Private Properties](#private-properties-nyt)
    * [Variables](#variables-nyt)
    * [Naming](#naming-nyt)
    * [Image Naming](#image-naming-nyt)
    * [Constants](#constants-nyt)
    * [Literals](#literals-nyt)
    * [Primitive Data Types](#primitive-data-types)
    * [Booleans](#booleans-nyt)
    * [Enumerated Types](#enumerated-types-nyt)
    * [Magic Numbers] (#magic-numbers)


* [General Code Quality] (#general-code-quality)
    * [Conditionals](#conditionals-nyt)
    * [Ternary Operator](#ternary-operator)
    * [Error handling](#error-handling-nyt)
    * [Singletons](#singletons-nyt)
    * [NSArrays] (#nsarrays)

* [Parsing] (#parsing)

* [Third Party Code] (#third-party-code)


Coding Standards And Style Guide / Common problems seen in project
<!--
//Things I'm not including but think I should:
//Strings/Copy should be in a localization file and not in line
-->


## Xcode project

The physical files should be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created should be reflected by folders in the filesystem. Code should be grouped not only by type, but also by feature for greater clarity. (NYT)

When possible, always turn on "Treat Warnings as Errors" in the target's Build Settings and enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas). (NYT)

## Documentation

#### Comments 
When they are needed, comments should be used to explain **why** a particular piece of code does something. Any comments that are used must be kept up-to-date or deleted. (NYT)

Block comments should generally be avoided, as code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. This does not apply to those comments used to generate documentation.

If large amounts of legacy code are being considered for being commented out, remember that legacy code is available through version control and therefore unnecessary to keep in line.  After the code is found to be unnecessary, simply delete it.

#### Apple Doc

Every new public method will have documentation in the .h

**For example:**
  ```objc

/**
 * Takes the basic data needed to populate the cell and sets the labels.
 *
 * @param topCopy the copy which will appear in the top label of the cell
 * @param bottomCopy the copy which will appear in the bottom label of the cell
 * @return void
 */
-(void) setCellLabels:(NSString*)topCopy wtihBottomCopy:(NSString*)bottomCopy;
  ```

#### Deprecation

When new code is written which invalidates or obsoletes old code, the old code will use the obsolete tag to denote the fact to developers.  Include a note to the developer about what to use instead and/or why the method is deprecated.

**For Example**
```objc
//Method returns an int instead of an NSInteger please use fooMethodWithParam instead.
- (int)fooMethod:(int)param __deprecated;

/**
* Method returns the same NSInteger which is passed in
* 
* @param param - passed in will be returned by method
* @return - NSInteger which was passed into method.
*/
- (NSInteger)fooMethodWithParam:(NSInteger)param;
```
## Formatting 

#### Line and Character Spacing

* Indent using 4 spaces. Never indent with tabs. Be sure to set this preference in Xcode.
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line. (NYT)
**For example (NYT):**

  ```objc
  if (user.isHappy) {
  //Do something
  }
  else {
  //Do something else
    }
  ```
* There should be exactly one blank line before each of the following:
  * An if statement 
  * A block
  * Before a method
  * A new group of functionality within a method, however, if a method has distinct groups of separate functionality, it should probably be broken apart into new methods.

#### Methods

* In method signatures, there should be a space after the scope (-/+ symbol). There should be a space between the method segments. (NYT)
* Keep methods short, methods should execute one idea, multiple ideas should be broken into smaller pieces.
* Methods will have names before the : for each parameter.
* Every method will have a return type
* Every parameter will have a type

**For Example (NYT)**:
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```

## Classes

* `@interface` lines will should be a space before and after each : 
* Every class should have an interface in the .m for private methods and properties
* Every new class written will user ARC

#### Init and Dealloc

`self` will not be used to access properties in init methods, this includes `self.variable` and `[self variable]`.

`dealloc` methods should be placed at the top of the implementation, directly after the `@synthesize` and `@dynamic` statements. `init` should be placed directly below the `dealloc` methods of any class. (NYT)

`init` methods should be structured like this (NYT):

```objc
- (instancetype)init {
    self = [super init]; // or call the designated initializer
    if (self) {
        // Custom initialization
    }

    return self;
}
```

#### Abstraction, Categories and Copied Code

Do not copy and paste more than 5 lines of code.  Code which needs to be reused should be made into a new method.  If the code needs to be reused across classes, consider creating a base class or category.

#### Multiple Classes in a File

Because the iTriage project has a large amount of data there are many cases where entire JSON objects are parsed into smaller objects which make sense to be visually grouped together in one file.  When there are multiple classes in a file, use `#pragma mark - ClassName` to distinguish the division.


## Properties / Member Variables
<!--Look up why dynamic should be used-->

#### Dot-Notation Syntax (NYT)

Dot-notation should **always** be used for accessing and mutating properties. Bracket notation is preferred in all other instances.

**For example:**
```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**Not:**
```objc
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

#### CGRect Functions (NYT)

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**For example:**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**Not:**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

#### Private Properties (NYT)

Private properties should be declared in class extensions (anonymous categories) in the implementation file of a class. Named categories (such as `NYTPrivate` or `private`) should never be used unless extending another class.

**For example:**

```objc
@interface NYTAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```
#### Variables (NYT)

Variables should be named as descriptively as possible. Single letter variable names should be avoided except in `for()` loops.

Asterisks indicating pointers belong with the variable, e.g., `NSString *text` not `NSString* text` or `NSString * text`, except in the case of constants.

Property definitions should be used in place of naked instance variables whenever possible. Direct instance variable access should be avoided except in initializer methods (`init`, `initWithCoder:`, etc…), `dealloc` methods and within custom setters and getters. For more information on using Accessor Methods in Initializer Methods and dealloc, see [here](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6).

**For example:**

```objc
@interface NYTSection: NSObject

@property (nonatomic) NSString *headline;

@end
```

**Not:**

```objc
@interface NYTSection : NSObject {
    NSString *headline;
}
```

#### Naming (NYT)

Apple naming conventions should be adhered to wherever possible, especially those related to [memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Long, descriptive method and variable names are good.

**For example:**

```objc
UIButton *settingsButton;
```

**Not**

```objc
UIButton *setBut;
```

A three letter prefix (e.g. `NYT`) should always be used for class names and constants, however may be omitted for Core Data entity names. Constants should be camel-case with all words capitalized and prefixed by the related class name for clarity.

**For example:**

```objc
static const NSTimeInterval NYTArticleViewControllerNavigationFadeAnimationDuration = 0.3;
```

**Not:**

```objc
static const NSTimeInterval fadetime = 1.7;
```

Properties and local variables should be camel-case with the leading word being lowercase. 

#### Image Naming (NYT)

Image names should be named consistently to preserve organization and developer sanity. They should be named as one camel case string with a description of their purpose, followed by the un-prefixed name of the class or property they are customizing (if there is one), followed by a further description of color and/or placement, and finally their state.

**For example:**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` and `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` and `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

Images that are used for a similar purpose should be grouped in respective groups in an Images folder.

#### Constants (NYT)

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro.

**For example:**

```objc
static NSString * const NYTAboutViewControllerCompanyName = @"The New York Times Company";

static const CGFloat NYTImageThumbnailHeight = 50.0;
```

**Not:**

```objc
#define CompanyName @"The New York Times Company"

#define thumbnailHeight 2
```

#### Literals (NYT)

`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating immutable instances of those objects. Pay special care that `nil` values not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.

**For example:**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**Not:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

#### Primitive Data Types
No longer use primitive data types as member variables, instead use NSInteger, CGFloat etc.

#### Booleans (NYT)

Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits.

This allows for more consistency across files and greater visual clarity.

**For example:**

```objc
if (!someObject) {
}
```

**Not:**

```objc
if (someObject == nil) {
}
```

-----

**For a `BOOL`, here are two examples:**

```objc
if (isAwesome)
if (![someObject boolValue])
```

**Not:**

```objc
if ([someObject boolValue] == NO)
if (isAwesome == YES) // Never do this.
```

-----

If the name of a `BOOL` property is expressed as an adjective, the property can omit the “is” prefix but specifies the conventional name for the get accessor, for example:

```objc
@property (assign, getter=isEditable) BOOL editable;
```
Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE).

#### Enumerated Types (NYT)

When using `enum`s, it is recommended to use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types — `NS_ENUM()`

**Example:**

```objc
typedef NS_ENUM(NSInteger, NYTAdRequestState) {
    NYTAdRequestStateInactive,
    NYTAdRequestStateLoading
};
```

#### Magic Numbers

Code should contain as few magic numbers as possible.  When magic numbers and other hard-coded variables are necessary declare them at the top of the file for easy changing.  Do not use `#define`

**Example:**
```objc
static NSString * const FONT_NAME = @"Helvetica";
static CGFloat const  NUMBER_TEXT_SIZE_IN_CIRCLE_PERCENTAGE = 0.40;
```

## General Code Quality

#### Conditionals (NYT)

Conditional bodies should always use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent [errors](https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256). These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) may happen where the line "inside" the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

**For example:**
```objc
if (!error) {
    return success;
}
```

**Not:**
```objc
if (!error)
    return success;
```

or

```objc
if (!error) return success;
```

#### Ternary Operator

Do not use Ternary Operators.   

#### Singletons (NYT)

Singleton objects should use a thread-safe pattern for creating their shared instance.
```objc
+ (instancetype)sharedInstance {
   static id sharedInstance = nil;

   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[self alloc] init];
   });

   return sharedInstance;
}
```
This will prevent [possible and sometimes prolific crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

#### NSArrays

Objects in an array should never be counted in a for loop unless the number of objects in the array is at risk of changing in the body of the loop:     

**For Example:**
```objc
int count = self.buckets.count;
for (int i = 0; i < count; i++) {
  NSLog(@"Pr-int:%d", i);
}
```

Counting the items this way means that the number of items in the array are counted each loop through, therefore the 

**Not:**
```objc
for (int i = 0; i < self.buckets.count; i++) {
  NSLog(@"Pr-int:%d", i);
}
```

## [Parsing] (#parsing)

ALL parsing will be done using the third party library: [JSONModel] (http://www.jsonmodel.com/)

The documentation is very helpful and is available here: [JSONModel Documentation] (https://github.com/icanzilb/JSONModel/blob/master/README.md#magical-data-modelling-framework-for-json).

All classes will subclass JSONModel will override the keymapper method.

**For Example:**
From [JSONModel Documentation] (https://github.com/icanzilb/JSONModel/blob/master/README.md#magical-data-modelling-framework-for-json)

Example JSON
```json
{
  "order_id": 104,
  "order_product" : @"Product#1",
  "order_price" : 12.95
}
```

OrderModel.h
```objc
@interface OrderModel : JSONModel

@property (assign, nonatomic) int orderId;
@property (assign, nonatomic) float orderPrice;
@property (strong, nonatomic) NSString* orderProduct;

@end
```

OrderModel.m
```objc
@implementation OrderModel

+(JSONKeyMapper*)keyMapper {
  return [JSONKeyMapper mapperFromUnderscoreCaseToCamelCase];
}

@end
```


<!--ADD DETAILS-->

<!-- THIS NEEDS TO HAVE DETAILS ABOUT WHAT LISCENSES WE ARE OKAY WITH
## [Third Party Code] (#third-party-code)
-->
