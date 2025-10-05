# Problem Statement
Frontline teams at United Airlines are responsible for ensuring every flight departs on time and is operationally ready. However, not all flights are equally easy to manage. Certain flights pose greater complexity due to factors such as limited ground time, higher volumes of checked or carry-on baggage, and specific customer service needs that often increase with passenger load.
Currently, identifying these high-difficulty flights relies heavily on personal experience and local team knowledge. This manual approach is inconsistent, non-scalable, and risks missing opportunities for proactive resource planning across the airport.
To address this, you are tasked with developing a Flight Difficulty Score that systematically quantifies the relative complexity of each flight using the datasets provided, which span two weeks of departures from Chicago O’Hare International Airport (ORD).
​​​​

## Objective
### The goal is to design a data-driven framework that:
- Calculates a Flight Difficulty Score for each flight using flight-level, customer, and station-level data.
- Identifies the primary operational drivers contributing to flight difficulty to enable proactive planning and optimized resource allocation.
 
## Deliverables
### 1. Exploratory Data Analysis (EDA)
- What is the average delay and what percentage of flights depart later than scheduled?
- How many flights have scheduled ground time close to or below the minimum turn mins?
- What is the average ratio of transfer bags vs. checked bags across flights?
- How do passenger loads compare across flights, and do higher loads correlate with operational difficulty?
- Are high special service requests flights also high-delay after controlling for load?

### 2. Flight Difficulty Score Development
- Build a systematic daily-level scoring approach that resets every day. Below are the outputs required:
- Ranking: Within each day, order flights by their difficulty score so that the highest-ranked flights represent the most difficult to manage.
- Classification: Group flights into three categories — Difficult, Medium, or Easy — based on rank distribution.

### 3. Post-Analysis & Operational Insights
- Summarize which destinations consistently show more difficulty.
- What are the common drivers for those flights?
- What specific actions would you recommend based on the findings for better operational efficiency?
 
### Feature Exploration Guidance
You are encouraged to explore and engineer features from across the datasets. Some examples of feature categories include:

- Ground time constraints: How scheduled ground time compares with expected turnaround standards
- Flight-specific characteristics: Number of checked bags, children on board, haul, aircraft etc.
- Passenger service needs: Requests such as wheelchair assistance or special seating
- These are not exhaustive—you should explore the datasets further to identify additional operational features that could influence flight complexity.
- Creative ideas are welcome! — feel free to explore additional approaches or external datasets

## Things to Note:

- Demonstrate your ability to write SQL/Python/R for the above stated tasks. Submit your code files along with instructions to run them.
- Summarize your approach, calculations and findings in a concise, data-driven presentation (7-8 slides)
- As this case study is intended to assess your critical thinking, individual analytical, and coding capabilities, you must not use generative AI tools such as ChatGPT, Claude, or Grok for assistance
 
## Submissions Guidelines:
### Submission Requirements
To submit your project, please provide the following in a single ZIP file:

- Report: A PDF or PPT file that includes your findings, calculations, and insights. You can use visualizations such as charts and diagrams to support your report.
Difficulty Score File: A CSV file named test_<your_name>.csv (e.g., test_johndoe.csv). This file must contain the following: flight details, features used for calculation, and final difficulty score (or class/rank order).
- Code: The Python, SQL or R scripts you used. Please include instructions on how to run them. If you used a code repository, you can share the link instead of the files.
- Additional Materials: Any other materials you used to create your report.

### Upload Instructions
- Combine all your files (the report, CSV, code, and any other materials) into a single ZIP folder.
- The total size of the ZIP file must not exceed 50 MB.
- You can save a draft of your submission before the final deadline.
- Drafts can be updated by uploading a new ZIP file.
- Once you submit your project, you will not be able to make any changes.
- Any drafts you have saved will be automatically submitted after the deadline.
​​​​​​​
**Important Notes** - Please note that there is no limit on the number of slides/pages. The structure of your report should include -table of contents, executive summary, headings, references, footnotes, and appendices.
 