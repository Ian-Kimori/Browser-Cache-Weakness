# Browser-Cache-Weakness

**complete, simple, practical checklist** that includes **EVERYTHING you need to verify browser cache weaknesses**, including:

*   Back‑button test
*   Offline mode
*   Response headers
*   Storage (LocalStorage, SessionStorage, IndexedDB, Cookies)
*   Autocomplete issues
*   Sensitive data stored anywhere in DevTools
*   History inspection
*   HTTPS caching rules

This is the **full audit checklist** you can use directly during assessments.

***

# ✅ **FULL BROWSER CACHE WEAKNESS TEST CHECKLIST**

Your goal is to make sure **the browser does NOT store sensitive data** (PCI DSS requirement).

Below is everything you must check.

***

# 🟦 **Step 1 — Log in normally**

*   Enter your username + password
*   Navigate to any page containing sensitive info  
    (Dashboard, profile, transactions, financial data, etc.)

This prepares the environment for testing.

***

# 🟦 **Step 2 — Back Button Test (VERY IMPORTANT)**

### 1. Log out.

### 2. Press the browser **Back** button.

### PASS:

*   Browser shows login page again
*   Or “Session expired” message
*   No sensitive info visible

### FAIL:

*   Dashboard/profile reappears
*   Old data is visible
*   Page loads from cache

> **If back button shows sensitive data → HIGH‑RISK FAIL**

***

# 🟦 **Step 3 — Offline Mode Test**

### How to do it:

1.  Press **F12** → DevTools
2.  Go to **Network** tab
3.  Tick **Offline**
4.  Refresh a sensitive page

### PASS:

*   Page does **NOT load**
*   Shows “offline” or asks you to log in

### FAIL:

*   Sensitive page loads while offline  
    → Stored in browser cache → **FAIL**

***

# 🟦 **Step 4 — Inspect Browser Storage (VERY IMPORTANT)**

Open DevTools:

**F12 → Application tab**

Now check:

## 🔹 **1. Local Storage**

Look inside:

*   `localStorage`

FAIL if you see:

*   Tokens
*   Usernames
*   Profile data
*   Session IDs
*   Cardholder data
*   Email
*   Anything sensitive

Local Storage = **persistent**, survives browser restart  
→ **very dangerous**

***

## 🔹 **2. Session Storage**

Check `sessionStorage`.

FAIL if:

*   Sensitive data stored
*   Session tokens stored here

Session Storage = temporary, but still insecure.

***

## 🔹 **3. Cookies**

Check under **Cookies**:

Look for:

*   `secure`
*   `httponly`
*   `SameSite`

### PASS:

*   `HttpOnly` is **true**
*   `Secure` is **true**
*   No sensitive data inside cookies
*   Only **session ID** is stored

### FAIL:

*   Tokens stored in cookie **without HttpOnly**
*   Tokens or data stored in **Plain text**
*   User data inside cookies

***

## 🔹 **4. IndexedDB**

Check if any stores contain sensitive content.

IndexedDB is used by some modern apps — but must NOT hold PCI data or tokens.

### FAIL if:

*   Tokens
*   User info
*   Payment data
*   Logs
*   Reports  
    → Found here

***

## 🔹 **5. Cache Storage**

Go to:
`Application → Cache Storage`

FAIL if:

*   Sensitive API responses cached
*   Full HTML pages cached

***

# 🟦 **Step 5 — Check Autocomplete Behavior**

Look at login & sensitive forms:

### PASS:

Input fields show:

    autocomplete="off"
    autocomplete="new-password"

### FAIL:

*   Browser auto-fills user data
*   Fields remember previous entries
*   Payment / personal data autocompletes  
    → Sensitive data stored locally

***

# 🟦 **Step 6 — Check Response Headers (The Most Important Technical Test)**

Open DevTools:

**Network → Click on any sensitive page request → Headers**

Look under **Response Headers**.

You want to see:

    Cache-Control: no-store, no-cache, must-revalidate
    Pragma: no-cache
    Expires: 0

### PASS:

All present.

### FAIL:

Any of these appear:

    Cache-Control: public
    Cache-Control: private
    Cache-Control: max-age=86400
    (no Cache-Control header)
    ETag: ...
    Last-Modified: ...

Means caching is misconfigured → **Fail**

***

# 🟦 **Step 7 — Test Private Browsing Mode (Optional)**

Open the site in:

*   Chrome Incognito
*   Firefox Private Mode

Repeat:

*   Back button test
*   Offline test
*   Storage test

Results should be the same.

***

# 🟦 **Step 8 — Check Data in Browser History**

### How to test:

1.  Open browser history
2.  Look at visited URLs

### FAIL if:

URLs contain sensitive data such as:

    /profile?email=user@test.com
    /transaction?card=1234

Sensitive parameters **must not** appear in the URL.

***

# 🟢 **FINAL PASS / FAIL DECISION CRITERIA**

### ✔ PASS (Secure)

If ALL below are true:

*   Back button does NOT show sensitive data
*   Offline mode does NOT load sensitive pages
*   Sensitive pages have `Cache-Control: no-store`
*   No sensitive data in:
    *   LocalStorage
    *   SessionStorage
    *   IndexedDB
    *   Cookies
    *   CacheStorage
*   Login and sensitive fields do NOT autocomplete
*   No sensitive data in URLs

### ❌ FAIL (Browser Cache Weakness)

If ANY of the following occur:

❌ Sensitive page loads after logout  
❌ Sensitive page loads in offline mode  
❌ No cache-control headers  
❌ Sensitive data stored in browser storage  
❌ Session data stored outside HttpOnly cookies  
❌ Browsers autofill sensitive data  
❌ Parameters in URL reveal sensitive info

***
