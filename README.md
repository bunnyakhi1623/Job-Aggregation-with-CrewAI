# Job-Aggregation-with-CrewAI
This project demonstrates a simple but powerful application of the CrewAI framework. It uses a team of specialized AI agents to automate the process of finding and aggregating job listings from a specific website, in this case, Google's official careers page.

By defining a series of sequential tasks, the agents work collaboratively to browse a website, filter results, and extract structured data, showcasing the potential of agentic frameworks for web scraping and data processing.

Features
Multi-Agent Collaboration: A "Senior Job Researcher" agent works with a "Job Aggregator" agent to complete a complex task.

Website Browsing: Utilizes a WebsiteSearchTool to navigate and read content from a specified URL.

Data Extraction: The aggregator agent is designed to parse unstructured HTML content and extract key information like job titles, locations, and application links.

Sequential Processing: Tasks are executed in a defined order, with the output of one agent serving as the input for the next.

Secure API Key Management: Uses python-dotenv to handle API keys securely via a .env file.

Prerequisites
Before running this project, ensure you have the following installed:

Python 3.8 or higher

pip (Python package installer)

Setup and Installation
Clone the repository:

git clone [your_repository_url]
cd [your_repository_folder]

Install the dependencies:

pip install crewai crewai[tools] google-generativeai python-dotenv
pip install langchain_google_genai

Set up your Google API Key:

Create a file named .env in the root directory of your project.

Add your Google API Key to the file in the following format:

GOOGLE_API_KEY="your_api_key_here"

You can obtain a Google API Key from the Google AI Studio or Google Cloud Platform.

Code Explained
Here is a line-by-line breakdown of the code from the Jupyter Notebook to help you understand its functionality.

Section 1: 

Initial Setup and Dependencies
!pip install crewai crewai[tools] google-generativeai python-dotenv
!pip install langchain_google_genai

!pip install: This command installs the necessary Python libraries for the project. crewai is the core library, crewai[tools] provides additional functionality, google-generativeai is for interacting with Google's models, and python-dotenv is for managing environment variables.

langchain_google_genai: This library provides the integration between Google's generative AI models and the LangChain framework, which is often used under the hood by CrewAI.

Section 2: 

Environment Variables and API Key Configuration
import os
from dotenv import load_dotenv
load_dotenv()
os.environ["GOOGLE_API_KEY"] = os.getenv("GOOGLE_API_KEY")

import os and from dotenv import load_dotenv: These lines import the modules needed to handle environment variables.

load_dotenv(): This function loads the variables from your .env file, making them accessible to your script.

os.environ["GOOGLE_API_KEY"] = os.getenv("GOOGLE_API_KEY"): This line sets the Google API key as an environment variable for the current session, which is a secure way to use your API key without hard-coding it in the script.

Section 3: 

Defining Tools for the Agents
from crewai_tools import WebsiteSearchTool

from crewai_tools import WebsiteSearchTool: This line imports a specialized tool that enables an agent to browse and search the content of a website.

Section 4: 

Instantiating Agents
from crewai import Agent
senior_job_researcher = Agent(
    role='Senior Job Researcher',
    goal='Identify relevant jobs based on provided skills, experience, and keywords from the official Google Careers website.',
    verbose=True,
    backstory=(
        "You are a Senior Job Researcher at a leading tech company."
        "You excel at navigating career websites and parsing job descriptions to find the best matches."
        "Your expertise lies in filtering through numerous listings to find opportunities that perfectly align with a candidate's profile."
    ),
    allow_delegation=False
)
job_aggregator = Agent(
    role='Job Aggregator',
    goal='Extract and structure job titles, locations, and application links from the raw HTML content provided by the Senior Job Researcher.',
    verbose=True,
    backstory=(
        "You are an expert data extractor and organizer."
        "You have a knack for turning unstructured data (like raw HTML) into clean, readable, and structured information."
        "Your precision is unmatched, ensuring that no critical details are missed."
    ),
    allow_delegation=False
)

from crewai import Agent: This imports the core Agent class from the crewai library.

senior_job_researcher: This agent is defined with a specific role and goal to search for jobs. Its backstory provides context, while verbose=True enables detailed logging.

job_aggregator: This agent is responsible for taking the raw output from the first agent and cleaning it up. Its goal is to extract and structure the data into a readable format.

Section 5: 

Defining Tasks for the Agents
from crewai import Task
google_job_search_task = Task(
    description=(
        "1. Browse the Google Careers website for jobs in the field of \"Software Engineering\"."
        "2. Filter the jobs by location: \"USA\" and \"India\"."
        "3. Focus on mid to senior-level positions and include jobs that are fully remote."
    ),
    agent=senior_job_researcher,
    tools=[WebsiteSearchTool(website="https://www.google.com/about/careers/applications/jobs/results/")]
)
job_aggregation_task = Task(
    description=(
        "Extract the following information for each job from the provided content:"
        "- Job Title"
        "- Location"
        "- Application Link"
        "Ensure the output is clean and well-structured, summarizing the findings in a clear, readable format."
    ),
    agent=job_aggregator
)

from crewai import Task: This imports the Task class, which defines a unit of work for an agent.

google_job_search_task: This task assigns a detailed description of the job search to the senior_job_researcher agent and provides it with the WebsiteSearchTool to perform the action.

job_aggregation_task: This task provides instructions for the job_aggregator agent on what information to extract from the content provided by the first agent.

Section 6: 

Creating and Running the Crew
from crewai import Crew, Process
job_search_crew = Crew(
    agents=[senior_job_researcher, job_aggregator],
    tasks=[google_job_search_task, job_aggregation_task],
    process=Process.sequential
)
result = job_search_crew.kickoff()
print(result)

from crewai import Crew, Process: This imports the Crew class, which orchestrates the agents and tasks.

job_search_crew: This line creates an instance of the Crew, passing in the list of agents and tasks. process=Process.sequential ensures the tasks are completed one after another.

job_search_crew.kickoff(): This is the method that starts the entire process.

print(result): This prints the final, aggregated output from the entire crew's operation.

Example Output:
The final output of the script will be a structured list of job titles and locations, aggregated by the job_aggregator agent:

Job Title: Software Engineer III, Google Cloud
Location: Sunnyvale, CA, USA
Application Link: Not available in the provided HTML content.

Job Title: Senior Software Engineer, Identity and Access, Full Stack
Location: Kirkland, WA, USA
Application Link: Not available in the provided HTML content.

Job Title: Software Engineer II, Calendar, Mobile
Location: Zürich, Switzerland
Application Link: Not available in the provided HTML content.

License
This project is licensed under the MIT License - see the LICENSE file for details.

The MIT License is a very permissive open-source license. It allows you to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the software. The only conditions are that you include the original copyright notice and this license text in all copies or substantial portions of the software.
