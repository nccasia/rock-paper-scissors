# **Rock, Paper, Scissors Game**

## **Features**
- Users can create game rooms, set up the number of players, and set the desired number of rounds.
- Only the room owner has the authority to start the game.
- Players can place bets using coins. The total bet amount is awarded to the champion based on the number of participants.
- Players can watch other players compete against each other after losing.
---

## **Project Setup**

- Install dependencies:
   ```bash
   $ npm install
   ```
- Start the project:
   ```bash
   $ npm run start
   ```

---

## **API & Mezon-Webview Integration**

### **1. Retrieve Current User Information**
- **Event**: `CURRENT_USER_INFO`
- **Purpose**: Fetch the user's information when they click on the app channel.
- **Usage**:
   ```js
   window.Mezon.WebView.onEvent("CURRENT_USER_INFO", () => {});
   ```

---

### **2. Send Bet Amount to the Bot**
- **Event**: `SEND_TOKEN`
- **Purpose**: Transfer the bet amount to the Bot for management when a user starts playing.
- **Usage**:
   ```js
   window.Mezon.WebView.postEvent("SEND_TOKEN", eventData);
   ```
- **Structure of `eventData`**:
   ```js
   eventData: {
       receiver_id: receiverId, // Bot ID
       amount: totalBet, // Total bet amount in the game
       note: `Đã đặt cược ${totalBet} token khi chơi game Rock Paper Scissors!`,
       sender_id: userId,
       sender_name: username, 
       extra_attribute: JSON.stringify({
         sessionId: currentGameId,
         appId,
       }),
   };
   ```

---

### **3. Claim Bonus Amount**
- **Purpose**: Use the Bot's API (specified in the `.env` file on the Backend) to distribute the bonus amount to the winner.
- **Implementation**:
   ```js
   await fetch(REWARD_URL, {
       method: "POST",
       headers: {
           apiKey: API_KEY,
           appId: APP_ID,
           "Content-Type": "application/json",
       },
       body: JSON.stringify({
           sessionId: currentGameId,
           userRewardedList: [{ userId: winner, amount }], // `amount`: Total bet amount in the game
       }),
   });
   ```
- **Example Response**:
   ```json
   {
       "message": "Successfully!",
       "sendSuccessList": [userId],
       "sendFailList": []
   }
   ```

---

## **Notes**
- Ensure that `REWARD_URL`, `API_KEY`, and `APP_ID` are correctly set up in the `.env` file.
- Always validate `eventData` before sending it to avoid runtime issues.
- The winner’s `userId` and the `amount` must align with the game's session data for successful reward distribution.
