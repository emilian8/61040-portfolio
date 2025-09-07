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

## Features
