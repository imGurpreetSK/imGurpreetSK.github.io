+++
date = '2019-06-08T02:23:11+05:30'
draft = false
title = 'Multi-caret in Android Studio'
author = "Gurpreet"
tags = ["android", "migrated-from-medium", "productivity", "IDE"]
+++

Knowing keyboard shortcuts is important. They save you time and help you achieve more in less time. If you need convincing, I highly recommend watching [‘Mouseless Driven Development’ by Hadi Hariri](https://vimeo.com/98922030).

This blog post is about one such feature of Android Studio (or any JetBrains IDE for that matter) which not a lot of people know about — multi-caret selection. A lot of my friends and colleagues who I pair-program with feel amazed by it and always ask “What was that!?”, so I decided to write about it to help them and everyone out there know about these.

Why use it? Because sometimes you need to replace just a few instances of a word/pattern and find & replace is not very useful.

-----

![Shortcuts](/assets/posts/multi-caret-in-android-studio/shortcuts.webp)

-----

### Alt + J / Control + G (Select next occurrence)

Select next occurrence of the currently selected text. If no text is selected, the current word with cursor is selected.

![Select-next-occurrence](/assets/posts/multi-caret-in-android-studio/select-next-occurrence.gif)

### Alt + Shift + J / Control +Shift + G (Unselect current occurrence)

Unselect current occurrence of selected text.

![unselect-current-occurrence](/assets/posts/multi-caret-in-android-studio/unselect-current-occurrence.gif)

### Alt + Shift + Ctrl + J / Control + Command + G (Select all occurrences)

Select all occurrences of the word in current file

![Select-all-occurrence](/assets/posts/multi-caret-in-android-studio/select-all-occurrences.gif)

### Alt + click & scroll / Option + click & scroll (Multiple cursors in column)

Select a column of text in current file

![multiple-cursors-in-column](/assets/posts/multi-caret-in-android-studio/multiple-cursors-in-column.gif)

### Alt + Shift + clicks / Option + Shift + clicks (Spawn caret at click position)

Spawn carets across file at click position, even in the same word.

![caret-at-click-position](/assets/posts/multi-caret-in-android-studio/caret-at-click-position.gif)

-----

Hope it helps!

*Originally posted on Medium [link](https://medium.com/@gurpreetsk/multi-caret-in-android-studio-ef9fe83f1758)*
