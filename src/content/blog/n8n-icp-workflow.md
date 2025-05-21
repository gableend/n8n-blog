---
title: "From Idea to ICP: My first n8n AI workflow"
description: "How I used n8n, OpenAI, and Google tools to automate Ideal Customer Profile (ICP) creation and analysis at scale."
date: 2025-05-21
---

As a marketer, I like turning raw data into actionable insights. I've been a heavy ChatGPT Pro user for some time now, using features like Deep Research, Operator, and CustomGPTs for different parts of the product marketer’s playbook. While powerful, I still find it challenging to establish repeatable processes. This all changed when I added n8n, an intuitive, low-code automation tool aligned perfectly with the snowballing agentic AI movement.

One area I've consistently struggled with in ChatGPT is reliable data manipulation and enrichment, especially at scale. Keeping insights up-to-date manually is cumbersome and error-prone. For example, in a fast-growing SaaS business, maintaining an accurate, real-time Ideal Customer Profile (ICP) is critical, as movements can be surprising.

This is the story of how, in just a few hours, I used n8n to to fully automate ICP creation using TheirStack, the mighty OpenAI API, and trusty Google tools (Sheets and Docs).


## 🧰 Toolkit overview

Choosing the right tools made all the difference:

- **n8n**: n8n: An intuitive, low-code workflow automation platform, allowing me to visually orchestrate tasks between APIs and tools.


- **TheirStack**: Provided detailed technology usage insights for firmographic enrichment and deeper understanding of market segments.


- **OpenAI API (GPT-4)**: Needs no introduction. Thanks to precise prompts honed in previous ChatGPT sessions, GPT-4 executed the final ICP analysis effortlessly. It also handled basic enrichment tasks, filling in missing industry, revenue, and employee data.


- **Google Sheets & Docs**: Google Sheets served as my primary datastore, while Google Docs neatly formatted the final ICP for easy sharing.


Together, these tools created a powerful, repeatable workflow.


## 🥞 TheirStack: My source of tech truth
To put my toolkit to the test, I decided to use n8n itself as my guinea pig technology. TheirStack maintains an extensive database of technology usage across thousands of companies, making it a great resource to find n8n customers.

I filtered and found data on thousands of companies currently using n8n. This dataset included details like industry, company size, revenue ranges, and geographic location, all essential firmographic elements for ICP analysis. Unfortunately, much like many data sources (I'm looking at you, Salesforce CRM!), the data was a little patchy. In my workflow, I needed to corral and enrich the data to fill the gaps.

<p style="text-align: center;"><b>TheirStack Company Search</b></p>

<center><img src="/images/01-theirstack-table.png" style="width:700px;" /></center>

## 🚦 Getting started with n8n

Getting started with n8n was easy. Just connect your Google account and you get access to their free tier. In fact, I was able to complete my entire project on this tier.

You start by creating a new workflow and establishing your trigger. If I were setting this up for real, I would have chosen a scheduled trigger to pull and refresh data periodically, or have triggered it via a Webhook when data changed. For my initial setup I went with a manual trigger to keep things simple.

The n8n UI is intuitive, and I was able to get going quickly. As a habitual ChatGPT user, I had it open on the side and relied on it to support me throughout the process. While there is an AI chatbot embedded in n8n, I felt more comfortable with the familiar GPT interface. I'll dig deeper into n8n's own AI capabilities as I build out future automations.

<p style="text-align: center;"><b>n8 Workflow Canvas</b></p>

<center><img src="/images/02-n8n-workflow-canvas.png" style="width:700px" /></center>



## 🎣 Pulling data from TheirStack

With n8n set up and ready to go, my next step was to pull the technology usage data directly from TheirStack. Conveniently, they provide an n8n integration button that lets you copy the required HTTP request and paste it directly to your n8n workflow, automatically creating the node for you.

Of course, things are rarely straightforward out of the gate. There was some initial messing about figuring out how to obtain my API Key and correctly add it to the node configuration. After a bit of trial and error, I got the connection working, only to realise I needed a paid TheirStack subscription to fully enable the API. Subscription sorted, I successfully pulled the data using the handy "Test Workflow" feature built into n8n.

With the data flowing, the next step was easy. I connected a Google Sheets node to my workflow. With just a few clicks, I mapped the JSON data fields to columns in my Google Sheet and was able to populate a single row.

At this stage, things got a little tricky. I wanted to populate 500 records, the maximum my TheirStack account would allow via the API (you have to be a superuser to do more), so I would have a representative set of n8n customers from major economies. I had some difficulties because I couldn't get my workflow to loop through each record in turn. I initially went down the road of using a "Loop Over Items" node to try and solve for this, but I made a breakthrough when I realized I could share the input and output data from a node with ChatGPT and providing some context. ChatGPT understood my issue and suggested using a Code node to break down my large dataset into individual records.

I haven’t really been let loose on code since my early days as a Java programmer back in the early 2000s, and aside from some basic scripting for demos in various pre-sales roles, my coding skills are basic. Thankfully, having GPT generate any necessary code worked just fine throughout this project. Sometimes it took a few attempts, but we always got there.

With that hurdle overcome, I had a fully populated Google Sheet containing my 500 records, ready for further enrichment and analysis.



## 🧼 Enriching the dirty data

With my dataset now safely stored in Google Sheets, the next challenge emerged: addressing the patchy or incomplete data I mentioned earlier. Missing or inconsistent data points such as industry classification, revenue, or employee counts dilute ICP analysis. I needed complete and consistent firmographic data to ensure accurate, meaningful insights.

To solve this I used a simple "If" node in n8n to check for missing data, routing any problematic records to an OpenAI node for enrichment. As ever, it was crucial to be explicit with GPT-4, ensuring it always used a consistent output format. After some trial and error, I had a prompt that worked every time.

<p style="text-align: center;"><b>GPT Enrichment Prompt</b></p>

<center><img src="/images/03-gpt-prompt.png" style="width:700px" /></center>



This structured approach allowed GPT-4 to reliably fill in gaps without adding unnecessary noise. Once enriched, I fed the cleaned data back into Google Sheets, resulting in a fully structured and reliable dataset. My previously "dirty" data was now clean, complete, and ready for ICP analysis.

<p style="text-align: center;"><b>Final n8n Enrichment Workflow</b></p>

<center><img src="/images/04-final-workflow.png" style="width:700px" /></center>

## 🧠 Summarizing data and running ICP analysis

With my clean list of 500 n8n customers and their firmographic details in place, the next step was to segment the data. Alongside basic counts by industry and country I needed to bucket the numerical data (revenue and number of employees) into segments like "Small, Medium, Large" for easy analysis. To do this I used a "Summarize" node to split the data as required. 

Soon I hit another roadbump in that OpenAI was struggling to make sense of the multiple datasets and would seem to hang thinking. With some help from n8n's AI assistant (which accesses documentation and community content) I was able to create a simple input for the LLM with an "Edit Fields" node and another Code node.

## ✨ AI-Powered ICP generation

With the data ready for analysis the next step was to have GPT do the ICP analysis. To do this I crafted the prompt shown below, referencing my summarized data. I kept it simple for illustration. Much more would be possible with richer 1st party input data.

<p style="text-align: center;"><b>GPT ICP Prompt</b></p>

<center><img src="/images/05-icp-prompt.png" style="width:700px" /></center>

The final step was automating the delivery of insights. By connecting n8n directly to Google Docs using OAuth, the AI-generated ICP analysis was formatted and ready for easy sharing. Note: In reality, I would spend more time on formatting, but as a proof of concept this was good to go.

<p style="text-align: center;"><b>ICP Analysis in Google Doc</b></p>

<center><img src="/images/06-gdoc-output-ICP.png" style="width:700px" /></center>



I did all of the summarizing and ICP analysis and Google Doc steps in a separate workflow. I'm sure I could have done it in one, but breaking it up kept the overall layout manageable.



<p style="text-align: center;"><b>Summarizing and ICP Analysis</b></p>

<center><img src="/images/07-second-workflow.png" style="width:700px" /></center>





## 🎓 Lessons learned

Using n8n to automate this workflow was next level vs using GPT as a copilot. Whilst I could (and have) got to similar outputs through prompting alone, and manually manipulating spreadsheets, using automation to solidify a repeatable workflow will be a game changer in time saved for this kind of analysis moving forward.

Key takeaways from this journey:

- **Prep is critical**: Enrich before you analyze.
- **n8n as glue**: Seamlessly connects tools, APIs, AI.
- **LLMs in automation**: Move beyond prompting—enable repeatable, scalable insight generation.



##  🏁 Final thoughts

My ICP analysis was simple, focusing only on firmographic data, as a proof of concept. In reality, to build out the full ICP, I would be bringing in multiple 1st party data sources, to augment 3rd party, and analysing technographics, psychographics, business situation and operating model too. 

n8n is clearly a powerful centerpoint for automating and scaling modern marketing operations. This leaves me with an excellent first impression of a platform positioned at the intersection of automation and AI. I’m looking forward to experimenting with some additional workflows to take on more of the product marketing playbook, like identifying use case clusters or supporting segmentation-driven GTM plays.

> As a product marketer, what excites me most is how n8n enables not just automation, but repeatable insight generation—unlocking many opportunities for faster GTM cycles, richer segmentation, and smarter decisions at scale.
