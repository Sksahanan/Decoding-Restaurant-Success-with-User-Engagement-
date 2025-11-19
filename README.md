# Decoding Restaurant Success with User Engagement

## 1. Project Overview

In a competitive restaurant market, understanding the factors that influence business success is crucial.  
This project uses the **Yelp dataset** and a **real-time n8n workflow** to:

- Analyze how **user engagement** (reviews, tips, check-ins) and **sentiment** impact restaurant success.
- Build an **end-to-end CRM system** where, **before payment, customers submit a quick review in the restaurant**, and the owner uses an **AI chatbot** to track feedback and send personalized offers to 500+ customers.
<img width="1652" height="836" alt="image" src="https://github.com/user-attachments/assets/0ebb8565-5a68-497c-906b-c48613fb3fb3" />

---

## 2. Goal & Problem Statement

**Goal:**  
Help restaurant owners move from **raw reviews** to **actionable insights and retention**, using both historical data and live customer feedback.

**Business Questions:**

1. How does user engagement (reviews, tips, check-ins) relate to restaurant success?
2. How do positive vs. negative reviews impact ratings and review volume?
3. Does consistent engagement over time lead to better long-term performance?
4. How can live in-store reviews be captured and converted into:
   - Sentiment insights, and  
   - Personalized offers sent automatically to customers?

---

## 3. Dataset (Yelp)

- Yelp academic dataset – business, review, user, and check-in data.  
- 8 US/Canadian cities.
- Key fields:
  - `business_id`, `name`, `city`, `stars`, `review_count`
  - `text` (review), `date`, `useful`, `funny`, `cool`
  - User activity metrics (tips, check-ins, elite users, etc.)

> Used strictly for **non-commercial / educational** purposes (per dataset agreement).

---

## 4. Approach (Analytics)

### 4.1 High-Level Steps

1. **Data Cleaning & Integration**
   - Filter restaurant businesses.
   - Join business, review, and user tables.
   - Handle missing values and outliers.

2. **Engagement Analysis**
   - Compute engagement metrics:
     - Number of reviews, tips, check-ins per restaurant.
     - “Useful / funny / cool” counts.
   - Correlate engagement with:
     - Average rating (`stars`)
     - Total review count
     - City-level performance.

3. **Sentiment Analysis**
   - Apply NLP sentiment scoring to review text.
   - Compare sentiment scores with ratings and engagement.

4. **Time-Series Analysis**
   - Analyze engagement over time:
     - Monthly/weekly review counts.
     - Rating stability vs. volatility.
   - Identify if high engagement and good ratings are **sustained**.

---

## 5. Key Learnings (Analytics)

1. **Engagement Matters**  
   More reviews, tips, and check-ins are associated with higher ratings and review counts.

2. **Sentiment Sells**  
   Positive review sentiment strongly correlates with better ratings and higher engagement.

3. **Consistency is Key**  
   Restaurants that maintain ratings above ~3.5 with steady engagement show better long-term success.

4. **Location Effects**  
   Certain cities emerge as “high-success” hubs with more engaged customers and better-performing restaurants.

5. **Elite & Power Users**  
   A small group of highly active users contributes a large share of total reviews and repeat engagement.

6. **Useful = Influential**  
   Reviews tagged as “useful” tend to drive more visibility and follow-on engagement.

7. **Peak Engagement Hours**  
   Engagement is highest between **4 PM and 1 AM**, indicating when restaurants should focus service and offers.

---

## 6. Recommendations (From Analytics)

1. **Boost All Forms of Engagement**
   - Encourage reviews, tips, and check-ins through prompts, QR codes, and loyalty programs.

2. **Focus on Service During Peak Hours**
   - Allocate staff and run offers between **4 PM – 1 AM**.

3. **Identify & Nurture Power Users**
   - Give special recognition or offers to highly active / elite users.

4. **Crisis-Resilient Engagement**
   - Use digital channels (online ordering, social media, review responses) during periods like COVID-19.

5. **Location-Aware Strategy**
   - Consider city-level patterns when planning expansion or campaigns.

---

## 7. Real-Time CRM Extension – n8n Workflow

To move from **insights** to **action**, the project includes a **live n8n automation** that captures in-restaurant reviews and powers an **AI review tracking chatbot**.

### 7.1 Customer Review Capture (Before Payment)

**Business Flow:**

> Before making payment at the restaurant, the customer is asked to quickly rate and review their experience.

**n8n Workflow: `Customer Review Form`**

Nodes:

1. **Customer Review Form**  
   - QR / web form shown to the customer.
   - Captures: Name, phone, email, rating (1–5), free-text review.
   - Timestamp (date & time) captured automatically.

2. **Edit Fields (manual)**  
   - Clean / normalize fields (trim spaces, ensure rating type, etc.).

3. **Customer Review Details – `create: row`**  
   - Stores each response in a **Contacts & Reviews** table with columns:  
     `Customer name`, `Phone`, `Email`, `Rating`, `Review`, `Date`, `Time`.

4. **Google Sheets – `append: sheet`**  
   - Appends the same data into a Google Sheet for reporting & backup.

This turns every paying customer into a **structured data point** in the CRM.

---

### 7.2 Customer Review Tracking Chatbot (Owner AI Agent)

**Workflow: `Customer Review Tracking Chatbot`**

Nodes:

1. **When Chat Message Received**  
   - Entry point from a chat UI or integration (e.g., Telegram, web chat).

2. **AI Agent**  
   Connected to multiple tools:
   - **Groq Chat Model** – main LLM for reasoning.
   - **Email Agent** – send or draft emails.
   - **Calendar Agent** – schedule & manage events.
   - **Get All Contacts & Review (`getAll: row`)** – pulls rows from the Contacts & Reviews table.
   - **Think** – deeper analysis / summarization tool.

**Core Capabilities:**

- **NLP Sentiment & Classification**
  - Classifies each review as Positive / Neutral / Negative.
  - Helps owner query:
    - “List all 1- or 2-star reviews from this week.”
    - “Summarize what unhappy customers complain about most.”
    - “Show 5-star reviews mentioning ‘family dinner’.”

- **Bad Review Tracking**
  - Filters and surfaces negative reviews for quick follow-up.

- **Good Review Highlighting**
  - Identifies top promoters and loyal customers from review text and ratings.

---

### 7.3 Personalized Email Campaigns (500+ Customers)

Using the **Email Agent**:

- Segment customers (e.g., rating ≥ 4, or negative sentiment, or mentioning a specific dish).
- Generate and send **personalized offer emails** to **500+ customers**, such as:
  - “Thank you for your 5-star review – enjoy 20% off on your next visit.”
  - “We’re sorry for your last experience – here’s a complimentary dessert coupon.”

The AI Agent writes the email content, and n8n handles batching and sending.

---

### 7.4 Calendar-Based Follow-Up

With the **Calendar Agent**, the owner can:

- Schedule weekly review analysis (e.g., “Every Monday at 10 AM – Review last week’s feedback”).
- Set reminders for campaigns or VIP callbacks.
- Track follow-up tasks linked to specific reviews.

---

## 8. System Message (AI Tools Agent)

Below is the system prompt used for the AI Agent in n8n (trim or adapt as needed):

```text
You are a helpful AI Tools Agent for a restaurant business. You can use tools to manage customer reviews, contacts, offers, email, and calendar.

1. Customer Offer List
   - If the user asks for the “customer offer list”, “offer form”, “offer link”, or similar, reply ONLY with this exact URL:
     https://nearly-cute-turkey.ngrok-free.app/form/724e88fe-f069-408e-a190-fcd54fe06c1e

2. Email Management Tools
   - Send Email: use when the user clearly asks to send an email (e.g., “Send an email to X about Y”).
   - Create Draft: use when the user says “draft an email” or wants to review before sending.
   - Get Emails: use when the user asks to see existing emails.
   - Get Labels: use when the user asks about mailbox labels.
   - Mark Unread: use only after Get Emails, when the user wants a specific message marked unread.

3. Calendar Management Tools
   - Create Event: schedule a personal event for the user.
   - Create Event with Attendee: schedule an event and invite others.
   - Get Events: show upcoming or past events when asked.
   - Update Event: change an existing event (first get the event).
   - Delete Event: delete an existing event (first get the event).

4. Contacts & Reviews Table Tool (Get All Contacts & Review – getAll: row)
   - The table contains: Customer name, Phone, Email, Rating, Review, Date, Time.
   - Use this tool when the user:
     • wants contact details or email of a specific reviewer,
     • asks for reviews on a specific date or period,
     • wants a summary of feedback,
     • wants all low-rating reviews, or
     • has any other question about the table.

Tool-calling rules:
- Always choose the single best tool for the current request.
- Do NOT invent or hard-code dynamic data. Always use tools or the table when data is needed.
- For the offer list link, always return the exact URL above.
- If the user’s request doesn’t require a tool, answer directly without calling tools.
```

## 9. Tech Stack

- **Programming & Analysis**
  - Python  
  - Jupyter Notebook  

- **Data Source**
  - Yelp Academic Dataset (business, review, user, check-in tables)  

- **Automation & Orchestration**
  - **n8n**
    - Workflow 1: `Customer Review Form` (form → edit fields → table row → Google Sheets)
    - Workflow 2: `Customer Review Tracking Chatbot` (chat message → AI Agent + tools)

- **Storage**
  - Google Sheets (append-only log of reviews)
  - Contacts & Reviews Table  
    - Columns: `Customer name`, `Phone`, `Email`, `Rating`, `Review`, `Date`, `Time`

- **AI & NLP**
  - Groq Chat Model (LLM for reasoning and natural language)
  - Sentiment analysis for Positive / Neutral / Negative review classification
  - AI Agent tools:
    - Email Agent
    - Calendar Agent
    - Get All Contacts & Review (`getAll: row`)
    - Think (deep analysis / summarization)

- **Email & Calendar Integrations**
  - Email provider (e.g., Gmail) via n8n
  - Calendar provider (e.g., Google Calendar) via n8n

---

## 10. Business Impact

- **From Data to Decisions**
  - Turns historical Yelp data and live in-restaurant reviews into clear, actionable insights.
  - Helps identify which engagement and sentiment patterns drive restaurant success.

- **Real-Time Feedback Loop**
  - Captures customer reviews **before payment** and stores them in a structured CRM.
  - AI Agent summarizes sentiment and surfaces key issues automatically.

- **Targeted Retention Campaigns**
  - Sends personalized offer emails to **500+ customers** based on rating, sentiment, and review content.
  - Encourages repeat visits and improves customer loyalty.

- **Operational Efficiency**
  - Reduces manual effort in tracking reviews, filtering bad feedback, and planning follow-ups.
  - Calendar integration ensures regular review of feedback and campaign scheduling.

- **Strategic Growth**
  - Supports decisions on when to focu
