User Interaction Document

Problem Statement:
We need to find the words from the images having word puzzle & highlight in the image as a agentic AI langflow output.

User Flow
1. We will collect all the random letters from the image.
   AI - Tool Call: To extract the Letters from the Image & Prepare the table.
2. We should know all the words present inside the image. For Ex: THANOS, BATMAN, SPIDERMAN, CAPTAIN AMERICA
   AI - Search the words inside the Table & Highlight.
   AI - Search the words in horizontal, vertical & diagonal manner for matching.
4. How to plot the puzzle words inside the LLM/Image
  Suggestion: we can prepare the plotting for word puzzle inside the table format in html. Ask LLM to find the word from the table & highlight it.
  AI - Prepare table with highlighted output in HTML.
   
Chat input (Image) --> Extract the Letters & keep in HTML Table format --> Search the Words inside the HTML Rows & Columns to make a word --> Highlight the words in different color inside the HTML & Give the output. 
Chat input --> 
