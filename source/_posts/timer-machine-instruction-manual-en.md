---
layout: post
title: (WIP)TimeR Machine Instruction Manual
date: 2019/1/22
categories: Android
---

> Complex Task Requires Complex App

This article is trying its best to introduce how to use TimeR Machine.

<!--more-->

## Table of Contents

1. [Basic Concepts](#Basic-Concepts)
1. [Timer Examples](#Timer-Examples)
1. [UI Instruction](#UI-Instruction)
1. [Differences from CycleTimer](#Differences-from-CycleTimer)
1. [Any trouble? Send a email](#Any-trouble?-Just-send-a-email)

## Basic Concepts

1. Timer

    This is NOT the traditional timer, but a task which contains several timers and loops.

1. Step

    This represents there is something to be done in a certain period.

1. Behavior

    A part of a step. It determines when this step is in progress, how the smartphone reminds you.

    - **A step without no behavior only counts down silently and goes to the next step silently. To get reminded when a step finishes, please add a step with behaviors.**
    - Current available behaviors: Music, Vibration, Screen (Pop up a reminding screen), Voice (Read the step name), Halt (Stay at this step until you move on manually), Beep (Play a beep every second)

## Timer Examples

### 1. Simple One

This is an exercise plan where we run and walk and repeat 3 times.

{% asset_img simple-timer.webp %}

Some steps have special colors. The first step and the last step are:

> Start Step and End Step: A timer may have loops and these two steps are the signals for the whole timer's start and end. Each of them will be executed only once at the start and the end of a timer.

"Timer Start" and "Timer End" is the beginning and finishing of the timer. They have behaviors like Music, or we don't know when this timer starts or finishes.

The next "Run" step has no behavior and simply counts down silently. After it, that's a "notifier":

> Notifier: A convenient remind step
> - It remembers what you've edited and the same step will be added the next time you add a notifier.
> - When you tweak time at a notifier step, the timer will go back to the previous step and set time to 1 minute (This can be turned off in the app settings).

In this way, we'll know when we should stop running and start walking.

This timer has 3 loops. After (run + notifier + walk + notifier) * 3, we'll enter end step and then our exercise plan completes🎉

### 2. Complex One

In this plan, we warm up, run and walk 13 times, long run and rest.

{% asset_img complex-timer.webp %}

Notice the timer loop is 1. Because our plan is too complicated to separate into loops. We need precise control.

{% asset_img complex-timer1.webp The 1st part %}

First, start step and warm up step use voice and beep behaviors. But, there is no notifier after them. Actually, some behaviors only run once at the start of a step, so we can use them to remind us that a step starts (of course you can add notifiers alternatively).

The next notifier indicates a "Group" is coming:

> Group：kind of a sub-timer. It has its own name and loop and can contain steps.

{% asset_img complex-timer2.webp The 2nd part %}

Here we put "Run" and "Walk" steps into the group and set loop to 13.

{% asset_img complex-timer3.webp The 3rd part %}

After the group, we add a step with halt behavior(Stay at this step until you move on manually) which is perfect for the situation where the time is indeterminate. After the long run, we'll go to the next step manually.

> Halt will make a timer stop counting down and ignore a step's duration.

The end step also acts as a step for resting.

## UI Instruction

- Add a step

    {% asset_img add-step1.webp %}

    {% asset_img add-step2.webp %}

- Remove a step

    {% asset_img remove-step.webp %}

- Add a behavior

    {% asset_img add-behavior.webp %}

- Edit a behavior

    {% asset_img edit-behavior.webp %}

## Differences from CycleTimer

In CycleTimer, reminders between steps are global and automatically added. You don't need to add them manually.

In TimeR Machine, you must add a step with behaviors or you'll never get reminded.

## Any trouble? Send an email

[Help & Feedback] - Scroll to the bottom - [Feedback] or [Here](mailto:ligrsidfd@gmail.com). You'll get replied within 24 hours.
