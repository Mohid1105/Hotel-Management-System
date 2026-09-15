# Mohid Grand Hotel — Management System

A command-line hotel management system built in Python. It allows staff to manage rooms, register guests, handle reservations, check guests in, order food and services, process checkout billing, cancel reservations, and view hotel statistics — all through a simple text-based menu.

## Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [How to Run](#how-to-run)
- [Menu Options](#menu-options)
- [Data Model](#data-model)
- [Business Rules](#business-rules)
- [Sample Workflow](#sample-workflow)
- [Known Limitations](#known-limitations)

## Features

- View all hotel rooms with type, price, and availability
- Register guests with age validation and guest category (Regular, VIP, Corporate)
- VIP guests are automatically prioritized at the front of the guest list
- Make room reservations by guest name, number of nights, and room type
- Check in guests against an existing reservation
- Order food and hotel services per room with running bill totals
- Checkout with automatic bill calculation, 5% tax, payment validation, and change calculation
- Cancel existing reservations with confirmation prompt
- View hotel-wide statistics (occupancy, guest counts, revenue, pricing extremes)

## Requirements

- Python 3.x
- No external libraries required (uses only built-in functions)

## How to Run

```bash
python hotel_management.py
```

The program will display a menu in the console. Enter the number corresponding to the action you'd like to perform. The system runs in a continuous loop until option `9` (Exit) is selected.

## Menu Options

| Option | Action | Description |
|--------|--------|-------------|
| 1 | View All Rooms | Lists every room with its number, type, price, and current availability status |
| 2 | Register Guest | Adds a new guest (name, age, category). Must be 18+ to register |
| 3 | Make Reservation | Books a room for a registered guest based on room type and nights |
| 4 | Check In | Checks in a guest with an active reservation and marks the room as occupied |
| 5 | Order Food & Services | Adds services (spa, breakfast, lunch, dinner, pool, massage) to an occupied room's bill |
| 6 | Checkout & Generate Bill | Calculates final bill (room + services + tax), processes payment, and frees the room |
| 7 | Cancel Reservation | Cancels an existing reservation after confirmation |
| 8 | Hotel Statistics | Displays occupancy, guest, and revenue statistics |
| 9 | Exit | Ends the program |

## Data Model

The system uses parallel lists (rather than classes or dictionaries) to track state:

### Rooms
| List | Description |
|------|-------------|
| `room_numbers` | Room number identifiers (e.g., 101, 102...) |
| `room_types` | Room type: `single`, `double`, or `suite` |
| `room_prices` | Price per night |
| `room_availablility` | Status: `available`, `reserved`, or `occupied` |

Rooms are organized across 3 floors:
- **Floor 1:** 101, 102, 103
- **Floor 2:** 201, 202, 203
- **Floor 3:** 301, 302, 303

### Guests
| List | Description |
|------|-------------|
| `guest_names` | Registered guest names |
| `guest_ages` | Registered guest ages (must be 18+) |
| `guest_catergories` | `regular`, `VIP`, or `corporate` |

### Bookings
| List | Description |
|------|-------------|
| `booking_guest_name` | Guest name tied to the booking |
| `booking_nights` | Number of nights booked |
| `booking_room_numb` | Room number booked |
| `booking_services` | List of ordered services per booking |

### Services Menu
| Service | Price |
|---------|-------|
| Spa | $50 |
| Breakfast | $30 |
| Lunch | $45 |
| Dinner | $45 |
| Pool | $35 |
| Massage | $50 |

### Revenue Tracking
`total_revenue` accumulates the total of all completed checkouts.

## Business Rules

- **Guest registration:** Guests must be 18 or older. VIP guests are inserted at the front of guest lists; all others are appended to the end.
- **Reservations:** Only registered guests can book. The requested room must exist, match the selected room type, and be available.
- **Check-in:** Requires a matching name and room number in the booking records. A room already marked `occupied` cannot be checked in again.
- **Services:** Can only be added to a room number that has an active booking. Running totals are recalculated and displayed after each service addition.
- **Checkout billing:** `Total = (Room Price × Nights + Services Total) × 1.05` (5% tax). Payments less than the total are rejected with the shortfall shown; overpayments return change.
- **Cancellations:** Require explicit confirmation before removing the booking and freeing the room.
- **Statistics:** Computed live from current list states — no stored/cached summary values.

## Sample Workflow

1. **Register a guest** → Option `2` → Enter name, age (18+), and category
2. **Reserve a room** → Option `3` → Enter guest name, nights, and room type
3. **Check in** → Option `4` → Enter guest name and room number
4. **Order services** → Option `5` → Enter room number, select services until finished
5. **Checkout** → Option `6` → Enter room number, review bill, select payment method, enter payment amount
6. **View statistics** → Option `8` → See updated occupancy and revenue figures

## Known Limitations

- Data is stored only in memory (Python lists) — all data resets when the program exits; there is no file or database persistence.
- No password/authentication layer; any user can access all menu functions.
- Relies on parallel lists indexed by position rather than structured objects (e.g., dictionaries or classes), which limits scalability and increases risk of index-mismatch bugs as the system grows.
- No input sanitization beyond basic type casting (`int()`, `float()`) — non-numeric input where a number is expected will raise an unhandled exception and crash the program.
- Guest names are not required to be unique, which can cause ambiguity when multiple guests share the same name during check-in or reservation lookup.
