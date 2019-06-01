---
layout: post
title:  "Notes and Reflections on Material Design"
date:   2018-08-12 21:34:00 -0500
categories: android material-design
---

# The Material Design Guidelines for Developers
As we all hopefully know, the [Material Design guidelines](https://material.io/design/introduction/#principles) are crafted by Google as a set of recommendations for creating better UIs for our end users. More specifically, the guidelines are meant to help us developers create intuitive, structured, and appealings UI within our apps. We should also be very well-aware that the Android ecosystem is incredibly *fragmented*, within millions of devices running all different variations of the Android OS.  

# Environment

### [Surfaces](https://material.io/design/environment/surfaces.html#properties)
* MD components are always 1dp thick -> they do not have varying degrees of thickness
* Adding content to a MD object does not increase it's thickness
* Material motion along the z-axis is typically due to user interaction

### [Elevation](https://material.io/design/environment/elevation.html)
* Some common elevations for popular MD components:
  * Nav drawer: 16dp
  * App bar: 4dp
    * Still struggling to understand the difference between `ActionBar` and `Toolbar`? Check out my previous article right [here](toolbar-actionbar-difference).
  * Card: 1dp to 8dp
  * FAB: 6dp
  * Button: 2dp to 8dp
  * Dialog: 24dp

* There is a table of default elevation values in the [MD guidelines](https://material.io/design/environment/elevation.html#default-elevations), along with a handy diagram:

![Diagram of default heights for popular MD components](/assets/screenshots/md-default-heights-diagram.png)

# Layout

### Understanding Layout
* Most measurements align to an 8dp grid, which aligns both spacing and the overall layout. This means that, as developers, we should use dimensions within our XML layouts in increments of 8dp.
* Smaller components, such as iconography and typography, can align to a 4dp grid.
* This should be (hopefully) *very* obvious: use dp to define your dimensions. Avoid defining your dimensions in px (pixels) at all costs.
* Likewise for fonts, define all font sizes in sp.
  * The difference between dp and sp is, according to the MD guide: *sp’s preserve a user's font settings. Users who have larger text settings for accessibility will see font sizes match their text size preferences.*
* MD talks about using a grid system for layouts, such that your content is contained within columns with gutters and margins providing space between the columns. 
* The number of columns used within a UI and the dimensions used for the gutters and margins is dependent on the screensize though: bigger screens can/should use more columns and potentially bigger gutters and/or margins.
* Different *breakpoints* define how many columns should be used. I.e., once the screen is *x* big, then it should have *y* columns. The breakpoints are [well defined in the spec](https://material.io/design/layout/responsive-layout-grid.html#breakpoints), along with some recommendations for gutters and margins.

### Spacing Methods
* Although text and iconography should align to a 4dp grid, there are exceptions to this, such as when text is centered within a button or list item:

![4dp Exception](/assets/screenshots/md-4dp-exception.png)

*  Keylines should be used for consistent placement of items outside of the aforementioned layout grid. Keylines are vertical, transparent lines that content items should align to. They provide spacing between your content and should also be sized in 8dp increments.
*  Padding is an alternative spacing method to keylines that's measured in either 4dp or 8dp increments. The advantage of padding is that it can be applied both vertically as well as horizontally.
* Another recommendation of MD is to use increments to size your UI elements. It can be any number, but it's recommended to use the height of a standard element (something that appears throughout your app) as the increment.
  * A good choice for your increment size would be the height of your app bar, since the app bar can be considered a standard element in your UI.
  * The increment is equal in height and width. Therefore, it can be used to size an element in both directions. 
  * Example: let's suppose you want to make a `CardView` 8 increments wide. If your app bar is 64dp tall, then the `CardView` would amount to being 64 x 8 = 512dp wide.
* Another way to maintain consistency throughout your app is to use consistent aspect ratios for your images and buttons. 
* MD recommends the following aspect ratios: 16:9, 3:2, 4:3, 1:1, 3:4, and 2:3.
* Touch targets are any elements that receive both touch and non-touch input.
  * A touch target should be at least 48dp x 48dp in size so that it can easily clicked on by the user. 
  * Use a minimum of 8dp spacing between touch targets.

### Component Behavior
Although there aren't any dimensions guidelines in [this section](https://material.io/design/layout/component-behavior.html#positioning-terminology), there is some great material in here on how to adjust your layout for larger screen sizes.


### Density
* When adjusting the line height of text elements, meaning the amount of space between lines of text, stick to using the 4dp baseline grid.

#Color 

### The Color System
* MD provides a color system in order to have an organized approach to applying color in a UI. 
* The system consists of a primary and secondary color, along with light and dark variants of both.
* Don't forget to touch back on how to actually impement this within the Material Design appcompt themes.
* The primary color is the color displayed most frequently throughout your app. 
  * You can also have *dark* and *light* variants of your primary color for additional distinction between elements. 
    * This additional distinction is frequently used in the Android UI, such as the status bar and app bar.
    * YOu can also use these variants to distinguish components within a single component, such as the background color of a FAB and the icon within it:

![Primary color variants](/assets/screenshots/md-primary-color-variants.png)

* The secondary color provides ways to accent UI elements. It is best applied to the following UI components:
  * The FAB
  * Selection controls like sliders and switches
  * Highlighting selected text
  * Progress bars
  * Links and headlines
* Just like the primary color, the secondary color can also have dark and light variants. 
  * As an alternative approach, you can color the FAB using the variants of your secondary color instead:

![Secondary color variants](/assets/screenshots/md-secondary-color-variants.png)

* Surface colors affect the background colors of UI components that contain content, such as cards, sheets, and menus.
* The background color is the color used behind scollable content. I.e., it contrasts with the surface color. 
* The error is used to indicate when an error occurs.
* There are also colors known as *on colors*, which are an interesting concept. On colors are used whenever UI components appear in front of surfaces that are using the other 5 aforementioned colors: primary, secondary, surface, background, and error.
  * They are typically applied to text, iconography, and strokes.
  * On colors should be chosen such that the UI component can be clearly seen against the containing surface's color (which would be one of the other 5 colors).
  * They are called "on" colors because they color elements that are placed "on" top of surfaces that are using the other 5 colors.
* There are 2 tools that can be used to choose colors for your UI:
  * [Material Palette Generator](https://material.io/tools/color/#!/?view.left=0&view.right=0)
  * [2014 MD Color Palettes](https://material.io/design/color/the-color-system.html#tools-for-picking-colors)

### [Text Legibility](https://material.io/design/color/text-legibility.html)
* All text should be legible and easy to read for the user
* Black text is recommended for use on light backgrounds. In contrast, white text is recommended for dark backgrounds. 
* Instead of using gray text on colored backgrounds, consider using white or black text with reduced opacity. This should create better contrast against the colored background.
* In order to create different levels of emphasis for black text against white backgrounds, consider the following:
  * High emphasis: 100% opacity
  * Medium emphasis and hint text: 60% opacity
  * Disabled text: 38% opacity

# [Typography](https://material.io/design/typography/the-type-system.html)

### The Type System
* MD includes a type scale that has a range of contrasting styles that support many different UI applications:

![MD type scale](/assets/screenshots/md-type-scale.png)

* Headlines are the largest text in the type scale. This text style should be used for short, important text or numerals.
  * Serif or sans serif fonts work well for headlines
* Subtitles are reserved for medium-emphasis text that is shorter in length.
  * As with headlines, serif and sans serif fonts work well for subtitles.
* Body text comes in ranges of 1-2 and it's typically used for long-form writing.
  * Serif or sans serif fonts work well for body text.
* Caption and overline text (text with a line above it) are the smallest text sizes. They are used sparingly to annotate imagery or introduce a headline.
* Button text is typically an all caps sans serif text.

### Understanding Typography
* Type aligns to the 4dp baseline grid
  * The *baseline* is the invisible line on which a line of text rests. 
* Specify distances from UI elements from the baseline.
  * A good approach to achieving this is to apply padding to your UI elements.
* Try to keep body text between 40-60 characters per line. 
