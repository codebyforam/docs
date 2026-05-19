# Booking Structure

## Collection

bookings/{bookingId}

## Booking Fields

- bookingId
- userId
- serviceId
- slotId
- fullName
- phoneNumber
- email
- place
- numberOfPeople
- preferredDate
- preferredTime
- status
- remarks
- createdAt
- updatedAt
- approvedBy

## Booking Flow

User selects service
→ User selects slot
→ User submits booking
→ Booking stored as pending
→ Admin reviews booking
→ Booking approved/rejected
→ User receives notification
