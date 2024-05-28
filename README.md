# Will AI Take Your Job?
## University of Chicago Applied Data Science Institute
## Natural Language Processing Final Project
## Instructor: Nick Kadochnikov

### Project Description
The recent renaissance in artificial intelligence (AI) is set to transform industries by automating tasks and enhancing employee efficiency. In fact, a 2023 Goldman Sachs report estimates that 25% of current work tasks can be automated by AI. [Why Goldman Sachs Thinks Generative AI Could Have a Huge Impact on Economic Growth and Productivity](https://www.aei.org/articles/why-goldman-sachs-thinks-generative-ai-could-have-a-huge-impact-on-economic-growth-and-productivity/)

Given a collection of ~200k news articles (~900 MB) focused on AI, Machine Leanring, and Data Science topics, the goal of this project is to identify what industries and jobs are going to be most impacted by AI over the next several years. These insights are invaluable as they can help workers develop strategies to leverage AI to elevate their work product, rather than having AI simply replace them.

### Tools & Techniques Used
Due to the size of the data, I use Google Cloud Platform's services - primarily Google Cloud Storage, VertexAI user-managed notebooks, and Compute Engine for a GPU when needed. For my slides, I use Google Slides with a template created by Slidesgo, icons by Flaticon, images by Freepik, and animated images by StorySet. I also employ a variety of natural language processing (NLP) techniques such as topic modeling, sentiment analysis and named entity recognition in my analysis.

### Project Outline & Methodology
- Step 0: Load Data
- Step 1: Data Cleaning - article titles and text are cleaned using regular expression pattern matching
- Step 2: Data Filtering - extremely long/short and irrelevant (lacking data science, machine learning and AI keywords) articles are filtered out
- Step 3: Topic Modeling - key topics and industries are identified using Latent Dirichlet Allocation (LDA) topic modeling from GenSim and ktrain libraries
- Step 4: Sentiment Analysis Preparation - trained an SVM sentiment analysis model on open-source corpus of ~4.6k news articles (from huggingface)
- Step 4.5: Sentiment Analysis - sentiment model applied at the sentence level and aggregated to determine overall article sentiment
- Step 5: Named Entity Recognition - NER is performed at the sentence level using spaCy's large enlish pipeline trained on written web text (en_core_web_lg)
- Step 6: Targeted Sentiment Analysis - article topics, entities, and sentiments are combined and their changes over time are analyzed
- Step 7: Analysis and Slides - all methodology, and insights/takeaways are presented in a slide deck

### Step 0: Loading the Data
I was provided ~200k news articles focused on data science, machine learning, and artificial intelligence topics in a parquet file. The articles were published between 2020-2024 and were collected using a web crawler. Features of the data set include article title, article text, url, publish date, and language (though all were in English). The raw article text was quite noisy because of the web scraping, so significant cleaning and filtering needed to be performed before the data could be analyzed.

### Step 1: Cleaning the Articles
- Media outlet name was removed from the article title if present
- URLs, links, special characters and exceptionally long words were removed from the article text
- Common web crawl remnants ('cookies', 'Contact Us', 'Subscribe', etc) were removed
- Website headings were removed

### Step 2: Filtering Out Irrelevant Articles
- Cleaned articles were tokenized
- Extremely long/short articles were filtered out (too short < (1/50)*mean token count | too long > 5000 tokens)
- Combined the clean title and text tokens and used nltk's FreqDist to find the top 100 most common tokens
- Chose top 10 AI, ML, data science keywords from the top 100 common tokens and filtered out articles missing these tokens in the first 20% of the article
- Top 10 keywords: AI, Data, Intelligence, Artificial, Technology, ChatGPT, Learning, Generative, Machine, Analytics
- Left with 193k cleaned & filtered articles

### Step 3: Topic Modeling
- I combine two topic modeling libraries - GenSim for identifying the ideal number of topics and ktrain for visualizing and diving deeper into the topics.
- GenSim LDA topic modeling was performed on a sample of 10,000 articles. Coherence score was calculated for n=2 to n=20 topics. Coherence score, a measure of how meaningful and interpretable the identified topics are, was maximized at n=10 topics with a score of 0.388.
- I then performed LDA topic modeling with ktrain using the optimal n=10 topics found by GenSim.
- I also used ktrain's TransformerSummarizer to summarize the 50 top-ranked articles for each topic.
- Analyzing the word distributions and summaries for each topic revealed six clear areas being impacted by AI.

**Topics:**
- Semiconductors
- Conversational AI
- Healthcare
- AI Regulation
- ChatGPT & LLMs
- Entertainment

### Step 4: Sentiment Analysis Model Training
- A custom SVM classifier was trained on the 'ML-news-sentiment' dataset from Hugging Face. The data set contains 4.6k news articles with positive, neutral, or negative labels
- I chose this model because it is trained on news articles and because it provides a neutral classification. Often, the factual or descriptive statement in news articles have no clear sentiment. Rather than innacurately classifying these statements as positive/negative, this model labels them as neutral.
- Articles were chunked into sentences using spaCy's sentence recognizer 'senter' and the customized sentiment model was deployed at the sentence level.
- Sentiment surrounding AI, ML, data science was overwhelmingly positive from the start of 2020 through 2023, though a waning count of articles written on these topics in early 2024 could warn of a potential "AI winter".

### Step 4.5: Sentiment Analysis
- Articles were grouped by topic (as identified by ktrain topic modeling) and their sentiment over time was analyzed.

The following are my sentiment analysis takeaways for each topic:
- Semiconductors: There was a sharp increase in positive sentiment in late 2023. Investigation of this trend revealed that NVIDIA saw a massive increase in stock price as demand for its AI-powering chips was reaching fever pitch.
- Conversational AI: Positive sentiment around conversational AI has risen hand-in-hand with LLMs as foundation models like ChatGPT, PaLM 2 and Gemini showcase strong question-answering capabilities - a key for conversational AI applications like customer service bots.
- Healthcare: Relative to other topics, healthcare has seen gradual growth in positive sentiment and has had more variation in negative sentiment. There is massive potential in the automation of administrative work, but data confidentiality concerns and regulation are slowing progress.
- AI regulation: AI regulation peaks in positive sentiment simultaneously with major OpenAI news. The first peak occurs in late 2022 with the release of ChatGPT. This suggests that ChatGPT's formidable capabilities spurred a rise in pro-regulation sentiment. Next, the highest peak occurs in conjunction with the ousting of Sam Altman as CEO of OpenAI. This set the AI acceleration vs. alignment debate alight.
- ChatGPT: After an initial massive spike in positive sentiment when ChatGPT was first released in late 2022, we see decreasing positive sentiment and high levels of neutral sentiment. This trend suggests that ChatGPT is losing its novelty and becoming a more mature constituent of the AI industry.
- Entertainment: Positive sentient regarding AI in the entertainment industry has largely followed breakthroughs in generative AI such as the releases of ChatGPT and the multimodal GPT-4. While breakthroughs, they raise thorny questions around intellectual property and threaten many creative jobs.

### Step 5: Named Entity Recognition (NER) to Identify the Organizations, Products and People Driving AI
- NER was performed using spaCy's "en_core_web_lg" model. A pipe with only "ner" enabled was used to perform NER at the sentence level.
- Organizations: The organizations mentioned most frequently include Google, Microsoft, Facebook and OpenAI. Unsurprisingly, these organizations are the world’s premier tech companies and are leading the way in AI R&D. ChatGPT is technically a product, but is so mainstream that it’s often referred to like a company.
- Products: Top AI, ML, DS products include YouTube, Galaxy, Bingand LinkedIn. YouTube is a major source for learning about AI, Bing was integrated with the Copilot chatbot in early 2023, and Galaxy phones by 
Samsung are one of the first to integrate AI on-device. LinkedIn is an important connector of top AI talent.
- People: OpenAI CEO Sam Altman, President Joe Biden, and Tesla and xAI founder Elon Muskare the largest figures in the AI space. Altman for his role mainstreaming Generative AI, Biden in his position of balancing AI acceleration and regulation at the national-level, and Musk as a general, and very outspoken, leader in tech.

### Step 6: Targeted (Entity) Sentiment Analysis
- NVIDIA: Positive sentiment around Nvidia jumped significantly in 2023. This reflects investors’ excitement around Nvidia’s positioning in the booming semiconductor manufacturing market.
- ChatGPT:  Positive sentiment around ChatGPT (entity) declined during 2023, despite the release of industry-leading GPT4. This shows that ChatGPT is losing hype as competitors have produced similarly performing models.
- President Biden: Sentiment regarding President Bidenis significantly mixed. While partially due to political polarization in the U.S., we do see a spike in negative sentiment in Oct. 2023 when Biden issued an executive order establishing AI safety and security standards.

### Step 7: Findings & Slides

**Recommendation 1:** Invest in semiconductor/chip manufacturing. Furthermore, learn how to use a GPU.
- While the semiconductor market has been red-hot, it is well warranted. Only a handful of companies like Nvidia,  AMD and Intel have the infrastructure to build these high performance chips. There is strong positive sentiment and demand is likely to increase as more AI products are built. Learn how to leverage this powerful tech before it replaces you.

**Recommendation 2:** Administrative and content generation roles are at particular risk of being automated by AI.
- The repetitiveness of administrative tasks and the sheer amount of training data available for content generationhave people excited about AI automation in these areas. If you are in such a role, embrace AIand use it to improve your work. You are already an expert and have a step ahead of everybody else. Don’t let them catch up by refusing to accept AI.

**Recommendation 3:** ChatGPT is losing its novelty and hype. Take this opportunity to separate yourself from others.
- While the hype of ChatGPT is dying down, it is still the most powerful tool the world has ever seen. Industries like healthcare and entertainment will experience major overhauls as new GPT iterations augment existing, or invent new, processes. Educate yourself about LLMs, learn the basics of integrating them into existing structures and then get creative. Use ChatGPT to create a new jobfor yourself, rather than waiting around for it to take yours.

You can find the slides with many more compelling visuals in this repository.

This was truly one of the most challenging, but also fulfilling, projects I have done. It made me much more confortable using GCP, gave me the opportunity to practice working with a large corpus of data and implementing many of the NLP concepts we had learned during the quarter, and also made me think deeply about many of the hot-topic issues in AI. Many thanks to my instructor Nick Kadochnikov for making this project possible.





