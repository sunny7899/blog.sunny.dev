---
title: The Ultimate Guide to FullCalendar in Angular From Setup to Drag-and-Drop
author: Sunny
pubDatetime: 2026-06-02T04:06:31Z
slug: fullcalendar-in-angular-from-setup-to-drag-and-drop
featured: false
draft: false
tags:
  - TypeScript
  - Astro
description:
  FullCalendar is the gold standard for JavaScript calendars, but integrating it with Angular’s strict typing and component structure can sometimes be tricky. This guide will walk you through building a robust calendar application with CRUD (Create, Read, Update, Delete) capabilities, drag-and-drop interactions, and a polished user experience.
---



## 1. Installation & Setup
First, install the core package, the Angular adapter, and the plugins you need (DayGrid for monthly views and Interaction for clicking/dragging).

npm install @fullcalendar/angular @fullcalendar/core @fullcalendar/daygrid @fullcalendar/interaction

## The Critical CSS Step
Many developers get stuck here. You must import the stylesheets globally.
Open your angular.json file and add these to the styles array:

"styles": [
  "src/styles.scss",
  "node_modules/@fullcalendar/core/main.css",
  "node_modules/@fullcalendar/daygrid/main.css"
]

Alternatively, add @import statements in your styles.scss.
## 2. Basic Configuration
In your app.module.ts, import the module:

import { FullCalendarModule } from '@fullcalendar/angular';

@NgModule({
  imports: [
    BrowserModule,
    FullCalendarModule // register FullCalendar
  ],
  // ...
})export class AppModule { }

In your component (app.component.ts), set up the options:

import { Component } from '@angular/core';import { CalendarOptions } from '@fullcalendar/core';import dayGridPlugin from '@fullcalendar/daygrid';import interactionPlugin from '@fullcalendar/interaction';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})export class AppComponent {
  calendarOptions: CalendarOptions = {
    initialView: 'dayGridMonth',
    plugins: [dayGridPlugin, interactionPlugin],
    editable: true, // Enable drag and drop
    selectable: true, // Enable date selection
    events: [
      { title: 'Meeting', date: '2025-08-05' }
    ]
  };
}

## 3. Managing Events (CRUD)## Adding Events
You can add events by modifying the array (good for bulk updates) or using the API (better for single additions).
Method A: The "Angular Way" (Immutable Array)

addEvent() {
  const newEvent = { title: 'New Task', date: '2025-08-06' };
  // Create a NEW array reference to trigger change detection
  this.calendarOptions.events = [...(this.calendarOptions.events as any[]), newEvent];
}

Method B: The "API Way" (Direct & Performant)
Use @ViewChild to access the calendar instance.

@ViewChild('calendar') calendarComponent: FullCalendarComponent;

addEventViaApi() {
  this.calendarComponent.getApi().addEvent({
    title: 'Direct API Event',
    start: new Date()
  });
}

## Updating & Deleting
Always use the Event API object provided by callbacks like eventClick.

handleEventClick(clickInfo: EventClickArg) {
  if (confirm(`Delete event '${clickInfo.event.title}'?`)) {
    clickInfo.event.remove(); // Removes it from the calendar
  }
}

To update, use setters:

clickInfo.event.setProp('title', 'Updated Title');
clickInfo.event.setDates('2025-09-01', '2025-09-02');

## 4. Drag and Drop Implementation
To make this work, you only need two things in your calendarOptions:

   1. editable: true
   2. eventDrop callback

calendarOptions: CalendarOptions = {
  // ... other config
  editable: true,
  eventDrop: (info) => {
    console.log(info.event.title + " was dropped on " + info.event.start.toISOString());

    if (!confirm("Are you sure about this change?")) {
      info.revert(); // Send it back if the user cancels
    } else {
      // Call your backend API here to save the new dates
    }
  }
};

## 5. Designing a Great UX (Interface Tips)
Don't rely on the default browser alerts (confirm()). For a professional app, use a Modal.

   1. On dateClick: Open a "Create Event" modal. Pre-fill the start date from the click arguments.
   2. On eventClick: Open an "Edit/Details" modal. Pass the event data to it.

UX Pro-Tip: If you are using Angular Material for your modals, you must handle the dependencies correctly (see the troubleshooting section below).
## 6. Troubleshooting Common Errors
This section addresses the specific errors you encountered while building this.
## Error 1: Module '"@fullcalendar/core"' has no exported member 'EventObject'
The Cause:
In FullCalendar v5+, the type EventObject was renamed/replaced. The documentation for older versions still references it, causing confusion.
The Fix:
Import and use EventApi instead.

import { EventApi } from '@fullcalendar/core';
// In your component or modalpublic currentEvent: EventApi;

## Error 2: Module not found: Error: Can't resolve '@angular/cdk/layout'
The Cause:
This error usually happens when you try to use Angular Material components (like MatDialog for your event popups) but haven't installed the Angular Component Development Kit (CDK). FullCalendar itself doesn't need this, but your UI library does.
The Fix:
Install the missing dependency:

npm install @angular/cdk

If you are using Angular Material, ensure you have the full package:

ng add @angular/material

## Error 3: Calendar events not showing up
The Cause: Mutating the events array without changing the reference.
The Fix:
Don't do this: this.events.push(newEvent)
Do this: this.events = [...this.events, newEvent]
------------------------------
## Conclusion
FullCalendar is incredibly powerful, but it requires a specific mindset: Let [Angular](https://angular.dev/) handle the layout, but let FullCalendar's API handle the data.
By using the EventApi for updates, installing the correct CSS, and wrapping your user interactions (like creation and deletion) in nice Angular Material modals, you can build a scheduling tool that feels like a native desktop application.


