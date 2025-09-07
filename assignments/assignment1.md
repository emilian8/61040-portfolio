# Assignment 1

## Domains

1. **Constraint-based scheduling** – assigning people to roles, shifts, or resources under constraints.  
2. **Event management** – planning budgets, attendance, and logistics for events.  
3. **Travel optimization** – finding affordable flights and having more robust search criteria.  
4. **Fitness and nutrition tracking** – structuring workouts, diet, and supplement planning.  
5. **Personalized Budgeting** – tracking savings and expense rates, also allowing for one time expenses.  
6. **Collaborative study tools** – coordinating problem sets, notes, and group study sessions.  
7. **Creative design workflows** – organizing and managing poster, flyer, and social media projects.  
8. **Health habit building** – creating sustainable routines around sleep, hydration, and exercise.  
9. **Assignment Organizer** – tool that assigns a certain number of hours for assignments depending on due date and importance.
10. **Traveling with friends** – coordinating group trips, splitting costs, and syncing itineraries.  

### Expanded Explanations of Three Selected Domains

#### 1. Constraint-based scheduling
I’m particularly interested in constraint problems because I often run into situations where multiple people need to be assigned to limited roles or resources under rules that must be respected. Constraint-based scheduling is a generalizable domain where software can genuinely reduce frustration and make planning more transparent. 

#### 2. Event management
This is an extension of constraint-based scheduling, but with a more specific use case of events, this could be used for large scale or small scale events, and would mainly focus on giving recomendations and providing a useful UI, this would mainly replace spreadsheets.

#### 3. Travel optimization
Often when searching for flights back home, I end up having multiple tabs open for different airports and days for which I am open to travel. I find that the current search options don't allow users to provide all the options that they have, and creating a solution for this could cut down on time spent searching for travel.

## Problems
### Constraint-based scheduling
1. **Fair role assignment** – ensuring people are assigned tasks without overloading or double-booking anyone.  
2. **Dynamic availability conflicts** – handling last-minute changes when people drop out or become unavailable.  
3. **Preference balancing** – accounting for individuals’ preferences (ex. some roles are more desirable than others).  

### Event management
1. **Party event management** – coordinating tasks, signups, and budgets for social events.  
2. **Attendance tracking** – logging who shows up and making sure required roles are covered.  
3. **Budget mismanagement** – overspending or losing track of expenses when planning larger events.  

### Travel optimization
1. **Broad availability flight searcher** – finding flights across multiple airports and flexible date ranges in a single query.  
2. **Multi-Origin Flight Searcher** – find flights that land at similar times from different origins (ex. group meeting in destination from different origin points).  
3. **Optimal Transport Mode Searcher** – comparing different modes of transport for a selected route (ex. bus vs. train vs. plane).

### Selected Problems

#### 1. Party event management
Large social events often require assigning roles, managing signups, and sticking to budgets. Right now, these tasks are often split across spreadsheets, group chats, and ad hoc communication, which makes it easy for things to slip through the cracks. A unified tool could simplify logistics and help planners ensure that events run smoothly.

#### 2. Broad availability flight searcher
When searching for flights, current platforms require travelers to manually check multiple airports and date ranges. This is frustrating for people with flexible schedules who just want the cheapest or most convenient option. A tool that accepts broader inputs (e.g., “any Boston-area airport” + “any time next weekend”) could significantly cut down on search time. I included this problem because I’ve personally run into it often, and existing solutions are weak in this area.  

#### 3. Fair role assignment
In many group settings (clubs, classes, events), people must be assigned roles under constraints like fairness and availability. Currently, this process is often manual, leading to uneven workloads or unhappy participants. Automating this would save time and ensure transparency. I included this problem because it is both generalizable (applicable beyond one niche) and technically interesting as a constraint satisfaction challenge.  

### Excluded Problems

- **Constraint-based scheduling**  
  - *Dynamic availability conflicts*: Excluded because while important, it’s mainly a reactive problem that might be better solved with strong communication, and could add unnecessary complexity to an MVP.  
  - *Preference balancing*: Excluded since modeling preferences can get very complex (almost requiring voting systems or negotiation), which may go beyond the scope of this project.  

- **Event management**  
  - *Attendance tracking*: Excluded because it feels narrow and less impactful; existing sign-in sheets or simple apps already solve this sufficiently.  
  - *Budget mismanagement*: Excluded since there are already many generic budgeting tools, and tailoring one specifically to events might not add enough unique value.  

- **Travel optimization**  
  - *Multi-Origin Flight Searcher*: Excluded since existing airline apps already do this reasonably well, and it might require complex integration with airline APIs.  
  - *Optimal Transport Mode Searcher*: Excluded because it does not have much use in the broader USA, since planes and cars dominate the modes of transport.

## Stakeholders

### 1. Party event management
**Stakeholders**
- **Event Planner** – the person or small team responsible for organizing the event.  
- **Attendees** – people who come to the event and depend on good planning for a smooth experience.  
- **Vendors** – third-party services who need clear coordination.  

**Impacts**
Event Planners are directly affected because disorganized logistics lead to stress, inefficiency, and a higher chance of mistakes. Attendees are indirectly affected if poor planning causes delays, long lines, or missing elements. Vendors rely on good communication to deliver services on time; when planning tools fail, miscoordination can cause financial loss or reputational damage.  

### 2. Broad availability flight searcher
**Stakeholders**
- **Traveler** – the user searching for flights with flexible airports/dates.  
- **Airline** – the carrier whose inventory may or may not be surfaced effectively in searches.  
- **Travel Aggregator** – platforms like Google Flights or Kayak that may compete with or integrate such a tool.  

**Impacts**
Travelers benefit most since the problem directly affects their ability to find affordable, convenient flights quickly. Airlines could be positively impacted if the tool helps fill seats by better exposing flexible options, but may also worry about price-sensitive shoppers reducing revenue. Travel Aggregators could feel competitive pressure but might also adopt such features to improve their services.  

### 3. Fair role assignment
**Stakeholders**
- **Group Organizer** – the leader responsible for assigning roles in clubs, fraternities, or teams.  
- **Participants** – members who receive roles and want fairness and transparency.  
- **Institution/Organization** – the larger body  that depends on smooth operations.  

**Impacts**
Group Organizers benefit because role assignment becomes faster, more systematic, and less contentious. Participants are directly affected — they care about fairness and not being overburdened, so automated role balancing can improve morale and participation. The Institution or Organization benefits indirectly: fairer, more efficient role assignment reduces burnout, improves reliability, and helps maintain member satisfaction and retention.  


## Evidence

### 1) Party event management

1. **Spreadsheet liability**: Event spreadsheets often become more of a liability than a lifeline. [Read more](https://www.pholeo.io/blog/the-hidden-risks-of-spreadsheets-in-conference-planning) 
2. **Spreadsheet risks**: Includes data-entry errors, calculation mistakes, security issues, and scalability limits. [Read more](https://www.oracle.com/business-analytics/spreadsheet-risks/)   
3. **Auditor warnings**: Over 90% of spreadsheets contain errors, with serious real-world consequences. [Read more](https://www.wired.com/story/spreadsheet-excel-errors) 
4. **Error quantification**: Operational spreadsheets show a 0.8%–1.8% formula error rate that sometimes causes critical issues. [Read more](https://arxiv.org/abs/0801.0715)   
5. **Compliance concerns**: Uncontrolled spreadsheets expose organizations to audit and regulatory risk. [Read more](https://arxiv.org/abs/0805.4211)
6. **Lack of oversight**: Studies show only ~13% of spreadsheets get audited; most users never received training. [Read more](https://en.wikipedia.org/wiki/Spreadsheet)
7. **Tab overload confusion**: Complex sheets slow planning and introduce confusion. [Read more](https://www.pholeo.io/event-management-resources)
8. **Spreadsheet risk growth**: Spreadsheets expand indefinitely, making change tracking nearly impossible. [Read more](https://www.mercur.com/Blog/spreadsheet-risks)  
9. **Spreadmart phenomenon**: Fragmented spreadsheets across stakeholders create conflicting data views. [Read more](https://en.wikipedia.org/wiki/Spreadmart)   
10. **Risk management necessity**: Systematic risk assessment templates are used to reduce event failures. [Read more](https://hirespace.com/c/ultimate-guides/risk-assessment) 

---

### 2) Broad availability flight searcher

1. **User frustration is real**: Travelers struggle with juggling multiple tabs to compare airports/dates. [User post](https://www.reddit.com/r/TravelProperly/comments/1j9ws0i/has_anyone_tried_a_flexible_flight_search_tool/) 
2. **PanFlights example**: A custom tool built to handle flexible, multi-destination flight search. [Read more](https://www.reddit.com/r/TravelProperly/comments/1j9ws0i/has_anyone_tried_a_flexible_flight_search_tool/)  
3. **Kiwi handles flex airports/dates**: Users note its ability to search with flexible airports and dates together. [Read more](https://travel.stackexchange.com/questions/174551/finding-a-flight-with-flexible-date-and-flexible-airports) 
4. **Google Flights limitations**: Supports flexible dates and multiple airports separately, but lacks a unified query interface. [Read more](https://www.going.com/guides/how-to-use-google-flights) 
5. **FlightsFinder aggregations**: Compares flexible-date flight tools and notes coverage gaps. [Read more](https://www.flightsfinder.com/flexible-dates) 
6. **Traveler tips echo fragmentation**: Guides advise manual “nearby airport” toggles—indicating UX gaps. [Read more](https://www.kayak.com/news/flexible-dates-nearby-airports/)  
7. **Flexible-date savings**: Even slight date shifts (±2 days) can save travelers hundreds of dollars. [Read more](https://www.dollarflightclub.com/articles/7-tips-for-finding-flights-with-flexible-dates/) 
8. **Flight search tool range**: Reviews of flexible-date tools show inconsistency in results and usability. [Read more](https://www.airfarewatchdog.com/blog/23754181/the-best-tools-for-flexible-date-searches/) 
9. **Multi-leg search complaints**: Forums show users often can't find systems that seamlessly combine dates and routing flexibility. [Read more](https://travel.stackexchange.com/questions/730/how-can-i-do-a-broad-search-for-flights)  
10. **Rick Steves advice**: Small travel date tweaks (e.g., weekdays vs. weekends) can yield major savings. [Read more](https://www.ricksteves.com/travel-tips/transportation/booking-flights) 

---

### 3) Fair role assignment

1. **NP-hard scheduling**: The nurse scheduling problem illustrates the complexity of fair assignment. [Read more](https://en.wikipedia.org/wiki/Nurse_scheduling_problem) 
2. **Fairness needs specialized algorithms**: Standard schedulers don’t ensure equity or fairness. [Read more](https://duch.mimuw.edu.pl/~ps219737/wp-content/uploads/2023/03/fairnessOfAlgorithms.pdf)  
3. **Volunteer scheduling pitfalls**: Lack of role matching and poor visibility into availability lead to issues. [Read more](https://www.socialroots.ai/blog/vms/scheduling-qualified-volunteers) 
4. **Scheduling failure costs**: Poor volunteer schedules lead to burnout, no-shows, and lost impact. [Read more](https://volunteerhub.com/blog/how-to-schedule-volunteers-effectively-best-practices)
5. **Volunteer coordination guide**: Highlights how mismanagement of shifts undermines program success. [Read more](https://www.galaxydigital.com/blog/volunteer-scheduling)   
6. **Best practices matter**: Guides recommend shift clarity, notifications, and automation to boost retention. [Read more](https://www.neonone.com/resources/blog/volunteer-scheduling/)
7. **Tech can fix volunteer chaos**: Custom tools help mitigate volunteer scheduling complexity. [Read more](https://optemple.com/blog/volunteer-scheduling)
8. **Spreadsheet spreadmart issues**: Volunteer scheduling via fragmented spreadsheets creates confusion. [Read more](https://en.wikipedia.org/wiki/Spreadmart)
9. **Humanitarian volunteer modeling**: In crisis contexts, robust optimization models are applied to volunteer assignment. [Read more](https://www.researchgate.net/publication/273579376_A_robust_optimization_approach_to_volunteer_management_in_humanitarian_crises) 
10. **High-risk spreadsheet exposure**: Errors in assignment logic (spreadsheet-based) can undermine fairness and correctness. [Read more](https://arxiv.org/abs/0801.0715) 


## Features

### 1) Party event management

**1. Centralized Task Dashboard**  
A dashboard where the Event Planner can assign roles, deadlines, and budgets in one place. This reduces reliance on scattered spreadsheets and makes it easier for vendors and attendees to see what’s expected of them.  

**2. Real-Time Notifications**  
Automated reminders sent to Attendees and Vendors about their tasks or shifts (e.g., setup at 5 PM, catering at 7 PM). This improves accountability and prevents last-minute chaos.  

**3. Budget Tracker with Alerts**  
An integrated budgeting tool that tracks event expenses in real time and alerts the Event Planner if they approach or exceed limits. This keeps spending transparent and ensures events remain financially sustainable.  

---

### 2) Broad availability flight searcher

**4. Multi-Airport + Date Query Input**  
A single search form where Travelers can input multiple airports and flexible date ranges at once. This eliminates the need to juggle tabs and saves time during trip planning.  

**5. Price Heatmap Visualization**  
An interactive calendar/heatmap that shows cheapest travel days and airports at a glance. This empowers Travelers to make cost-conscious choices while also encouraging Airlines to fill less popular flights.  

**6. Smart Alerts and Recommendations**  
Travelers can set broad “intents” (e.g., “any weekend in October from Boston area to California”). The system then sends notifications when qualifying flights appear. This helps aggregators capture flexible travelers who might otherwise abandon searches.  

---

### 3) Fair role assignment

**7. Constraint-Aware Assignment Engine**  
An automated engine that balances fairness, availability, and workload when assigning roles. Group Organizers save time, while Participants gain confidence in equitable treatment.  

**8. Transparent Assignment Report**  
A simple visual report showing why each Participant received their role (e.g., based on past workload, availability). This promotes trust and helps the Institution demonstrate fairness in decision-making.  

**9. Quick Swap and Reassignment Tool**  
A lightweight interface for Participants to swap roles (with Organizer approval). This reduces no-shows, accommodates last-minute conflicts, and ensures coverage while maintaining fairness.  

