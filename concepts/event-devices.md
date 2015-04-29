---
currentMenu: event_devices
---

# Overview
Event devices are where the magic of Evflow happens. Since the event loop is completely event-agnostic, the event loop uses event devices to listen for different types of events. The event loop contains the loop-specific code, while event devices contain the event-specific code for polling for events and selecting streams that are ready for reading or writing.
