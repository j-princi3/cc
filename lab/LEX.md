## ✅ **Lex Chatbot Setup Steps**

---

### 🔸 Step-by-Step

1. **Go to Amazon Lex → Create Bot**

   * **Bot Name**: e.g., `HotelReservationBot`
   * Language: English
   * **Leave default settings** (no Lambda for now)

2. **Create Intent**

   * Example: `MakeReservation`
   * This intent handles a user goal, like booking a hotel.

3. **Add Utterances**

   * Example:

     * "I want to book a room"
     * "Make a hotel reservation"
     * "Book hotel"

4. **Create Slot Types** (custom or built-in)

   * Example:

     * `RoomType`: single, double, suite
     * `CheckInDate`: built-in date type

5. **Add Slots to Intent**

   * These are the inputs needed from user:

     * RoomType (What kind of room?)
     * CheckInDate (When?)
     * NoOfNights (How many nights?)

6. **Add Prompts**

   * Lex will ask:

     * “What type of room would you like?”
     * “When do you want to check in?”

7. **Add Confirmation Prompt**

   * e.g., “Do you want to book a single room from July 2 for 3 nights?”

8. **Fulfillment**

   * What happens after all info is gathered.
   * Options: Return message or trigger **Lambda function**.

9. **Add Another Intent**

   * e.g., `CancelReservation`
   * Add utterances like:

     * “Cancel my booking”
     * “I want to cancel reservation”

10. **Click "Build" and then Test the Bot**

---

## 💬 Amazon Lex Key Terms (Short & Simple)

| Term                    | Meaning                                                               | Example                                         |
| ----------------------- | --------------------------------------------------------------------- | ----------------------------------------------- |
| **Bot**                 | Main container for conversation                                       | `HotelReservationBot`                           |
| **Intent**              | A task the user wants to do                                           | `MakeReservation`                               |
| **Utterance**           | User phrases that trigger intent                                      | “Book a room”, “Reserve a hotel”                |
| **Slot**                | Data Lex needs to fulfill intent                                      | Room type, date, nights                         |
| **Slot Type**           | Format/type of slot data                                              | Custom (`RoomType`) or built-in (`AMAZON.DATE`) |
| **Prompt**              | Lex’s questions to gather info                                        | “What kind of room?”                            |
| **Confirmation Prompt** | To confirm before action                                              | “Should I book it?”                             |
| **Fulfillment**         | Final step, action after info                                         | Return message or call Lambda                   |
| **Lambda Function**     | A backend function (in Python/Node.js) that can process booking logic | e.g., Save booking to DynamoDB                  |

---

## ⚡ Lambda in Lex (Short)

* Optional.
* Triggered **after slots are filled**.
* Used for:

  * Validating slot values
  * Performing backend tasks (like booking in DynamoDB)
  * Returning dynamic responses

---
