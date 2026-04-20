# Weight Tracker - An app for tracking weight goals and progress

- **Briefly summarize the requirements and goals of the app you developed. What user needs was this app designed to address?**

  This app addresses the need for simple, private weight-progress tracking. Users can create an account, set a goal weight, log daily weight entries, and visualize their trend over time with a line graph. The app also sends a push notification when a user reaches their goal, providing positive reinforcement. The core user needs were secure multi-user support, persistent data storage, clear progress visualization, and a low-friction data-entry experience.

- **What screens and features were necessary to support user needs and produce a user-centered UI for the app? How did your UI designs keep users in mind? Why were your designs successful?**

  Five screens were built: a combined Login/Signup screen (using a ViewSwitcher to avoid navigation clutter), a Setup screen for first-time goal entry, a Dashboard showing the weight-history table and trend graph, and a Settings screen for adjusting the goal weight and notification preferences. Material Design components, including cards, a navigation rail, and a DatePickerDialog, were used throughout to match Android platform conventions users already know. The dashboard was designed as a single scrollable view so all key information (graph + history table) is one scroll away, reducing taps and cognitive load.

- **How did you approach the process of coding your app? What techniques or strategies did you use? How could those techniques or strategies be applied in the future?**

  I used a layered MVVM-style architecture: plain model classes, a singleton Repository that owns all SQLite access, lightweight ViewModels that delegate to the repository, and Activities that only handle UI and user events. This kept database logic out of the Activities entirely, making each layer independently readable. Password storage was implemented with PBKDF2WithHmacSHA256 (100,000 iterations + random salt) rather than plain text or a weak hash, a pattern directly applicable to any future app that stores credentials. The Repository singleton pattern also means database connections are opened once and reused, which is a practical strategy for any data-driven Android app.

- **How did you test to ensure your code was functional? Why is this process important, and what did it reveal?**

  Testing was done manually through direct device/emulator execution, walking through each user flow: account creation, duplicate-username rejection, goal setup, weight entry and deletion, the graph rendering for edge cases (single entry, multiple entries on the same day, all entries at the same weight), and the notification trigger when goal weight was crossed. This process revealed that the graph library needed special handling when all data points shared the same x-value (same-day entries), which led to the intelligent x-axis spacing logic in `DashboardActivity.populateGraph()`. Manual testing is important because automated unit tests verify logic in isolation but cannot catch UI layout breaks, graph rendering edge cases, or permission dialog behavior on real hardware.

- **Consider the full app design and development process from initial planning to finalization. Where did you have to innovate to overcome a challenge?**

  The most significant challenge was the weight-trend graph. The GraphView library expects evenly distributed x-axis values, but users can log multiple entries on the same day or log nothing for days at a time, producing duplicate timestamps or large gaps. The solution was to pre-process entries in `populateGraph()`, where multiple entries sharing the same date, they are spread across a synthetic sub-day interval so each point gets a unique x-coordinate without distorting the visual trend. This required moving from a direct timestamp-to-x mapping to a calculated-index approach.

- **In what specific component of your mobile app were you particularly successful in demonstrating your knowledge, skills, and experience?**

  The data and security layer stands out. `WeightTrackerRepository` implements the Repository pattern as a proper singleton, centralizing all CRUD operations and keeping SQL entirely out of the UI layer. `WeightTrackerDatabase` uses a well-structured SQLiteOpenHelper with foreign-key relationships across three tables. `PasswordUtils` applies industry-standard salted PBKDF2 hashing, the same algorithm used in production authentication systems, rather than taking a shortcut. Together these demonstrate a strong understanding of data architecture, separation of concerns, and secure credential handling in a mobile context.

## Launch Plan

The goal of launching the WeightTracker application is to deliver a simple and reliable tool that helps users stay consistent with tracking their weight over time. The application focuses on core functionality such as logging daily weight, tracking progress toward a goal, and providing feedback through notifications. A structured launch plan ensures the application is ready for users and meets platform expectations.

**App Presentation:** The store description will emphasize usability and consistency, specifically how quickly users can log their weight, review their history, and stay accountable to a personal goal, without relying on technical language. The app icon will follow a clean, minimal design (a scale or trend line) using a limited blue/green palette to communicate health and consistency, remaining recognizable at small sizes.

**Platform Support:** The minimum supported version is Android 8.0 (API 26), targeting the most current SDK available. Testing was performed on the Android Emulator across multiple API levels to confirm consistent behavior in UI rendering, database interactions, and activity lifecycle handling.

**Permissions:** Only one permission is required: `POST_NOTIFICATIONS` on Android 13+. It is requested at runtime only when the user enables notifications in Settings. If denied, the app continues to function normally, as optional features do not block core functionality.

**Notifications:** A notification fires only when a user crosses their goal weight threshold, not on every entry below the goal. This prevents notification fatigue and makes the milestone feel meaningful. Users can toggle notifications on or off at any time from the Settings screen.

**Monetization:** The app will launch completely free to lower the barrier for new users and build an initial user base. Monetization options, such as a paid tier with extended history/enhanced tracking or non-intrusive ads, will be evaluated after real-user feedback is collected. Any approach will prioritize keeping the interface clean and simple.

## Goals

- Distribute through the Google Play Store following a soft launch to a smaller user group
- Gather usability feedback and resolve minor issues before full public release
- Monitor for crashes, performance issues, and user feedback post-launch
- Maintain compatibility with new Android versions through ongoing updates

## Continued Work

Future enhancements planned after launch, driven by real user behavior:

- Improved data visualization (richer graph options, date range filtering)
- Additional goal tracking options (body measurements, milestone history)
- Refinements to the notification system
- Evaluate monetization strategy once user base is established
- AI-powered meal and workout planning personalized to the user's current weight, goal weight, and progress trend