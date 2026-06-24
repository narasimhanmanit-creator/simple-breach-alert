BreachAlert - Personal Data Breach 
Monitoring Service 
BreachAlert is a privacy-focused, high-reliability personal data exposure monitor designed to 
notify users when their credentials appear in public data dumps. The system is engineered 
defensively to respect upstream rate limits using an asynchronous serialized queue and 
short-term caching. 
�
�
 Security & Privacy-First Architecture 
Unlike traditional breach checkers that store raw database dumps, BreachAlert utilizes a 
Zero-Retention Metadata-Only Cache protocol to protect consumer data privacy. 
       [ USER REGISTRATION ] 
                 │ 
                 ▼ 
     [ 6-Digit Email OTP ] ──(Ensures Ownership / Prevents Stalking) 
                 │ 
                 ▼ 
       [ WATCHLIST TRIGGER ] 
                 │ 
         ┌───────┴───────┐ 
         ▼               ▼ 
   [ REDIS CACHE ]   [ SQL DB ] ──(Only records Breach ID metadata) 
     (Checks 24h) 
         │ 
    (Cache Miss) 
         ▼ 
[ SERIALIZED TASK QUEUE ] 
         │ 
         ▼ 
[ ASYNC BACKGROUND WORKER ] ──(Processes tasks strictly sequentially) 
         │ 
   (1.6s Delay) 
         ▼ 
  [ HIBP SECURE API ] 
 
1. Ownership Verification (Prevention of Stalking/Harassment) 
To prevent malicious actors from monitoring email addresses they do not own (cyberstalking), 
a monitored email address remains in an inactive state and will not be scanned until the owner 
enters the unique, cryptographica ly secure 6-digit verification OTP dispatched to that address. 
2. Upstream Rate Limit Preservation (Task Serialization) 
The Have I Been Pwned (HIBP) API strictly enforces rate limits (maximum 1 request per 1.5 
seconds). To handle high concurrent request volumes without dropping connections, 
BreachAlert implements: 
● A Redis Cache Check: Intercepts scans. If an email has been scanned in the past 24 
hours, the cached state is returned immediately, bypassing the external API ca l. 
● An Asynchronous Task Queue: Tasks that cannot be answered by the cache are pushed 
to a Redis LIST. The background queue worker pops tasks out sequentialy, inserting a 
strict 1.6-second execution delay between each API cal. 
3. Actionable Mitigation Matrix 
When a breach is identified, our system translates impact classes into actionable steps: 
● Passwords Exposed: Recommends generating random credentials inside an encrypted 
password manager immediately. 
● Financials/SSN Exposed: Prompts the user to instantly freeze credit files at major credit 
bureaus. 
● Phone Numbers Exposed: Warns of an expected rise in targeted SMS phishing attempts. 
⚡
Quick Start (Local Setup) 
Prerequisites 
● Node.js (v18 or higher) 
● Python (3.11 or higher) 
● Redis (Local or Cloud instance) 
Backend Deployment 
1. Navigate to the backend directory and instal Python dependencies: 
cd backend 
python -m pip instal -r requirements.txt 
2. Launch the FastAPI server: 
python -m uvicorn main:app --reload 
3. (In a separate terminal) Launch the asynchronous Redis queue processor: 
python worker.py 
Frontend Deployment 
1. Navigate to the frontend directory and instal Node modules: 
cd frontend 
npm instal 
2. Start the hot-reloading development server: 
npm run dev 
3. Open your browser to http://localhost:5173/
