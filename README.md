# Android App Scheduler

A system-grade Android scheduling engine that enables users to launch installed applications at precise times while maintaining a complete and immutable execution audit trail.

This project demonstrates deep understanding of Android’s background execution model, exact alarm scheduling, activity launch restrictions, persistence strategies, and clean architectural design under modern Android constraints.

---

# 🎯 Problem Statement

Modern Android imposes strict limitations on:

- Background execution
- Exact alarm permissions
- Activity launch behavior
- Doze mode
- Battery optimizations

This project solves the following:

- Precisely schedule installed apps to launch
- Allow editing and cancellation
- Support multiple schedules without time conflicts
- Persist execution records
- Survive device reboot
- Log success, failure, and cancellation with reasons

This is not just an alarm app.

It is a **mini job orchestration system built for Android.**

---

# 🏗 Architecture

The project follows **Clean Architecture + MVVM**, emphasizing separation of concerns, scalability, and testability.

## Layered Structure

            ┌───────────────────────────┐
            │       Presentation        │
            │  ViewModels / UI State    │
            └───────────────┬───────────┘
                            │
                            ▼
            ┌───────────────────────────┐
            │          Domain           │
            │  UseCases / Business Rules│
            └───────────────┬───────────┘
                            │
                            ▼
            ┌───────────────────────────┐
            │            Data           │
            │ Repository + Room DB      │
            │ Alarm Scheduler Wrapper   │
            └───────────────┬───────────┘
                            │
                            ▼
            ┌───────────────────────────┐
            │       Android System      │
            │ AlarmManager              │
            │ BroadcastReceiver         │
            │ PackageManager            │
            └───────────────────────────┘

---

# 📦 Core Features

## 1. App Scheduling
- Select installed apps via `PackageManager`
- Schedule launch at exact date/time
- Prevent duplicate time conflicts
- Persist schedule in database

## 2. Schedule Management
- Modify scheduled time
- Cancel pending schedule
- View active schedules

## 3. Execution Logging (Immutable Audit Trail)

For every scheduled execution, the system records:

- App name & package
- Scheduled time
- Actual execution time
- Execution status:
  - SUCCESS
  - FAILED
  - CANCELLED
- Failure reason (if applicable)

Execution logs are immutable and stored separately from schedules.

---

# 🗄 Data Model Design

The system separates user intent from execution outcome.

## ScheduleEntity (User Intent)

- id

- packageName

- appName

- scheduledTime

- isActive

- createdAt

- updatedAt

Represents what the user scheduled.

---

## ExecutionLogEntity (System Outcome)

- id
- scheduleId
- packageName
- scheduledTime
- executedAt
- status (SUCCESS | FAILED | CANCELLED)
- failureReason (nullable)


Represents what actually happened.

This design ensures:

- Historical traceability
- No destructive updates
- Audit compliance
- Future analytics support

---

# 🔄 Execution Flow

User schedules app
│
▼
Schedule saved in Room DB
│
▼
AlarmManager registers exact alarm
│
▼
Alarm fires → BroadcastReceiver
│
▼
Attempt to launch target app
│
├── Success → Log SUCCESS
│
└── Failure → Log FAILED + reason





