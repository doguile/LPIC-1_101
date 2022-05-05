---
description: '106.3: Accessibility v2  Weight: 1'
---

# Chapter 5: Accessibility

<details>

<summary>Key terms</summary>

`Braille Display` The ability to display text as Braille for blind users is possible with the britty software package and a refreshable Braille display device

`High Contrast/Large Print Desktop Themes.` Themes can be selected that are easier to view for visually impaired users such as the High Contrast or High Contrast Inverse

`Mouse keys` A setting that allows the mouse pointer to be moved around the screen by using the keyboard.

`On-Screen Keyboard` A graphical version of a keyboard that is presented on-screen for the user to use by way of a pointing device or mouse.

`Screen Magnifier` A program that allows you to zoom in on the screen and specify a zoom ratio

`Screen Reader` A text-to-speech program.

`Slow/Bounce/Toggle keys` Slow Keys is a keyboard setting that will only accept each keypress if a key is held down for a specific period of time. Bounce Keys help prevent a key from being repeated if it is pressed again too quickly after the first time it is pressed. The Toggle Keys feature provides audio feedback when keyboard modifier keys, like Shift, Alt, and Ctrl, are pressed.

`Sticky/Repeat keys` Sticky keys is a keyboard setting that allows the user to perform key combinations such as Ctrl+C without having to hold both keys at once. Repeat Keys setting is to prevent the user from accidentally repeating keys if they type too slowly.

</details>

## Introduction

Accessibility is the **process of making a standard system available to those who have difficulties or disabilities**. Accessbility also may include those not experiencing a system through traditional channles, i.e, from a mobile environment.

Improved accessibility for users within the GUI environment can be achieved by modifying audio settings, visual settings, and other assistive technologies.

## Accessibility Utilites

The K Desktop Environment has a software package named `kdeaccessibility` which includes the following utilites:

* A screen magnifier called _KMagnifier_ (<mark style="color:red;">`kmag`</mark>) . This allows you to zoom in on the screen and specify a zoom ratio.
* An automatic clicking tool called KMouseTool (<mark style="color:red;">`kmousetool`</mark>)
* A text-to-speech screen reader called KMouth (<mark style="color:red;">`kmouth`</mark>)

## Keyboard Accessibility Settings

Within the GNOME Desktop Environment, the main dialog for accessibility settings is opened by clicking the following:

&#x20;               **Application > Systems Tools > Settings > Universal Access**

Several keyboard settings can be enabled by going to the Keyboard Accessibility settings of the GNOME desktop:

* Repeat Keys
* Sticky Keys
* Slow keys
* Bounce keys
* Toggle keys
* Mouse keys

### Repeat Keys

The poin of the Repeat Keys setting is to **prevent the user from accidentally repeating keys if they type too slowly**

### Sticky Keys

It allows the user to perform key combinations such as **Ctrl + C** **without having to hold both keys** at once. Instead, the user can press the **Ctrl** key and then press the **C** key to effectively type **Ctrl + C**.

### Slow Keys

Slow keys are the opposite of Repeat Keys, so both should not be enabled together. With slow keys, **it will only accept each keypress if a key is held down for a specific period of time**. Slow keys are for users who have "heavy hands" and accidentally press keys while moving from one key to another.

### Bounce Keys

Bounce Keys help **prevent a key from being repeated if it is pressed again too quickly** after first time it is pressed.

### Toggle Keys

The Toggle Keys feature initially **provided audio feedback when keyboard modifier keys**, like **Shift**, **Alt**, and **Ctrl**, are pressed. It also can provide feedback when other accessibility features are used.

Users may get frustrated trying to find Toggle Keys as there is nothing that is actually labeled Toggle Keys in the Typing Assist dialog. However, the Audio Feedback box on the Typing Assist window

### Mouse Keys

The mouse pointer can be moved around the screen by using the keyboard.

## Visual Theme Settings

The Adwaita theme is used by default for the GNOME desktop, but other themes can be selected that are easier to view for visually-impaired users such as the High Contrast or High Contrast Inverse themes. To select a theme that will affect the colors used for the desktop environment, click on the following:

1. **Application > Accessories > Tweaks > Appearance Tab**

The size of the fonts used for various items can be adjusted to improve the visibility of items within the desktop environment. To adjust the fonts while using GNOME, click on the following:

1. **Applications > Accessories > Tweaks > Appearance > Fonts Tab**

## **Assistive Technologies**

Numerous assistive tehcnologies exist to help users interact with a graphical desktop environment:

### Braille Display

The ability to display text as Braille **for blind users** is possible with the <mark style="color:red;">`brltty`</mark> software package and a refreshable Braille display device. The Orca screen reader also has the capability to enable the output of Braille

### Screen Reader

For the GNOME desktop environment, the Orca screen reader is used as the preferred application.

### On-Screen Keyboard

For users who may not be able to type on a normal keyboard, a graphical version of a keyboard can be presented on-screen for the user to use by way of a pointing device or mouse
