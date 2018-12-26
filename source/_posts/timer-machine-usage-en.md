---
layout: post
title: TimeR Machine Usage
date: 2018/12/19
categories: Android
---

TimeR Machine Usage

<!--more-->

1. Step

    A step represents something is going to be done during within a certain time. A newly created step without any settings simply count down silently and then move to the next step or finish the timer.

    If you wish to know when a step finishes, you have to add another step as a reminder after it and add some effects like music, vibration, etc(behaviors).

    Every step's duration, behaviors and name can be modified.

    - Some Special Steps

        - Notifier: it has a different color(blue by default); it saves your changes and show the same content next time you add a notifier; when you tweak time at this kind of step, the timer will go back to the last step and set time to 1 minute(this can be turned off in the app settings).

        - Start and End: there can be multiple loop for a timer and these two steps can be signs for timer start and end. They execute only once at the start and end of a timer. Besides, when a timer is terminated by a scheduler, the timer will jump to the end step.

1. Differences between TimeR Machine and Cycle Timer

    The biggest difference is in the TimeR Machine, you have to add reminders manually. Othewise, there won'y be any feedback.

    Besides that, in the CycleTimer, the remind duration is globally set in the app settings. In the TimeR Machine, a reminder is a part(step) of a timer. Its duration and ringtone requires to be set manutally.
