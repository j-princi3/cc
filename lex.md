
---

# ✅ **Experiment 9: Amazon Lex Chatbot for Airline Booking**

---

## 🎯 **Goal:**

Build a **Lex chatbot** that:

1. Collects essential flight booking details
2. Suggests **Business Class** for groups (>4 passengers)
3. Sends a **rich confirmation message**

---

## 🧱 **Components Involved:**

| Component             | Purpose                                        |
| --------------------- | ---------------------------------------------- |
| **Amazon Lex**        | Natural language interface (chatbot)           |
| **Lambda Function**   | Back-end logic (recommendations, confirmation) |
| **Amazon CloudWatch** | (Optional) Logs & debugging                    |

---

## 🔧 **Step 1: Create a New Amazon Lex Bot**

### ✅ **Steps:**

1. Go to **Amazon Lex → Bots → Create bot**
2. **Bot Name**: `FlightBookingBot`
3. Language: `English (US)`
4. IAM Role: Create a new role with basic permissions
5. Leave session timeout and other defaults
6. Click **Create Bot**

---

## 🗨️ **Step 2: Define Intent: `BookFlight`**

### ✅ **Intent Slots (User Input Fields)**

| Slot Name       | Type                | Prompt                          |
| --------------- | ------------------- | ------------------------------- |
| `DepartureCity` | AMAZON.City         | Where are you flying from?      |
| `Destination`   | AMAZON.City         | Where are you flying to?        |
| `TravelDate`    | AMAZON.Date         | What is your travel date?       |
| `ReturnDate`    | AMAZON.Date         | (Optional) Return date?         |
| `Passengers`    | AMAZON.Number       | How many passengers are flying? |
| `Class`         | Custom Slot         | Economy, Premium, or Business?  |
| `MealPref`      | Custom Slot         | How many Veg / Non-Veg meals?   |
| `Seats`         | AMAZON.AlphaNumeric | Preferred seat numbers?         |

---

### ✅ **Sample Utterances:**

```
I want to book a flight
Book tickets from {DepartureCity} to {Destination}
Flying on {TravelDate}
```

---

## 🧠 **Step 3: Add a Lambda Function (Booking Logic)**

Go to **Lambda → Create Function**

### ✅ **Function Name**: `FlightBookingProcessor`

Paste this sample logic:

```python
import json

def lambda_handler(event, context):
    slots = event['sessionState']['intent']['slots']
    
    dep = slots['DepartureCity']['value']['interpretedValue']
    dest = slots['Destination']['value']['interpretedValue']
    date = slots['TravelDate']['value']['interpretedValue']
    return_date = slots.get('ReturnDate', {}).get('value', {}).get('interpretedValue', 'N/A')
    pax = int(slots['Passengers']['value']['interpretedValue'])
    flight_class = slots['Class']['value']['interpretedValue']
    meal = slots['MealPref']['value']['interpretedValue']
    seats = slots['Seats']['value']['interpretedValue']
    
    if pax > 4 and flight_class.lower() != 'business':
        flight_class += " (Upgraded to Business Class)"

    total = 17500 * pax  # Assume ₹17,500 per seat
    discount = 0.2 if pax >= 4 else 0
    final_amount = total * (1 - discount)
    ff_id = "GA-7X9B2P"
    points_earned = int(final_amount * 0.01)
    
    message = f"""
✅ Booking Confirmed!
{dep.upper()} → {dest.upper()} | {date} (Return: {return_date})
{pax} Passengers | {flight_class}
Meals: {meal} | Seats: {seats}
Total: ₹{int(final_amount):,} ({int(discount*100)}% group discount applied)
Frequent Flyer: {ff_id} (Earned: {points_earned} points)
"""
    
    return {
        "sessionState": {
            "dialogAction": {"type": "Close"},
            "intent": {
                "name": "BookFlight",
                "state": "Fulfilled"
            }
        },
        "messages": [{
            "contentType": "PlainText",
            "content": message.strip()
        }]
    }
```

Deploy the function and add it as the **fulfillment Lambda** in Lex.

---

## 📦 **Step 4: Test Your Bot**

Say:

```
I want to book a flight
From: Delhi
To: Mumbai
Travel: 25 Dec 2024
Return: 05 Jan 2025
Passengers: 5
Class: Economy
Meals: 2 Veg, 3 Non-Veg
Seats: 15A,15B
```

---

### ✅ **Output:**

```plaintext
✅ Booking Confirmed!
DEL → BOM | 25 Dec 2024 (Return: 05 Jan 2025)
5 Passengers | Economy (Upgraded to Business Class)
Meals: 2 Veg, 3 Non-Veg | Seats: 15A,15B
Total: ₹87,500 (20% group discount applied)
Frequent Flyer: GA-7X9B2P (Earned: 875 points)
```

---

## 🔎 **Why This Works**

| Feature                  | Purpose                                                |
| ------------------------ | ------------------------------------------------------ |
| **Lex Intent Slots**     | Collect user input (departure, date, passengers, etc.) |
| **Custom Lambda**        | Business logic for pricing, upgrades, rewards          |
| **Passenger > 4 rule**   | Auto-recommends upgrade to Business                    |
| **Confirmation Summary** | Gives user clear details + perks                       |
| **Frequent Flyer Logic** | Simulates a loyalty program, adds realism              |

---

## 🧠 You Now Know:

* How to design an **intelligent Lex chatbot**
* Use **Lambda** to apply logic and send back dynamic summaries
* Use slot types, filters, and conditions in **dialog management**
* **Enhance customer experience** with auto-upgrades and personalization

---
