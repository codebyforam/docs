# Services Structure

## Collection

services/{serviceId}

## Purpose

Services module controls:
- Booking behavior
- Slot availability
- Capacity rules
- Approval flow
- Time duration
- Booking limitations

## Service Fields

- serviceId
- name
- description
- bookingMode
- allowMultipleBookings
- maxCapacity
- slotDuration
- requiresApproval
- isActive
- createdAt
- updatedAt

## Booking Modes

- individual
- group
- batch
