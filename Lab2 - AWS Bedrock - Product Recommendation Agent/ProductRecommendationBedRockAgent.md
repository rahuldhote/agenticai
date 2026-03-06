**Goal:**
  Build an ecommerce chatbot recommending the products for various occasion like birthday,personal events adding it in cart & recommend the personalized gift wrapping option to end user.

  *Technology Stack*: AWS Services like IAM Roles, Cloudformation, Bedrock, Lambda, Dynamo DB & S3.

**High Level Design**

![alt text](design.png)

**Agent Components & Responsibilities:**

_Input Function:_
- Input will be received from end user through chat input component.
- Input should be passed to LLM to generate the specific keywords.
- It should give specific keywords comma seperated as a output in capital letters. For ex: KITCHEN,FOUNDATIONS,DRAINPIPE

_Base 64 Image Conversion Agent:_
- A word grid image specified from particular URL should be fetched by this agent.
- It should convert this image content to Base64 using LLM Call.
- It should give output a Base64 Format.

_Convert Base64 to 2D JSON Agent_
- Output text obtained from base64 conversion agent should be received as a input.
- This input should be pass to LLM to convert Base64 text into 2D letter JSON GRID.

_Words Finder & Highlighter Component_
- 2D JSON Grid obtained from above agent should be received as input.
- Keywords obtained from input component also should be taken as input.
- Keywords should be searched in JSON & will be highlighted in HTML format.
- HTML Format with highlighted keywords should be given as Output.

_User Output Component_
- This component should display the HTML with Higlighted keywords in Word GRID puzzle.

**Langflow Diagram:**

<img width="1063" height="532" alt="image" src="https://github.com/user-attachments/assets/43ea67bf-ef6d-460f-9a21-242abf7d6f1d" />


**Output:**

<img width="884" height="366" alt="image" src="https://github.com/user-attachments/assets/55700842-b75c-4883-86b1-ec68a3684ec8" />
<img width="621" height="232" alt="image" src="https://github.com/user-attachments/assets/d0c24a78-be06-499e-9593-8d5ab804fa7a" />
<img width="618" height="246" alt="image" src="https://github.com/user-attachments/assets/e8b3569c-6bfa-463c-81ec-a4806f87df4d" />

**Langflow Export**

[WordPuzzleExport.json](https://github.com/user-attachments/files/25475267/WordPuzzleExport.json)


This steps will be help to implement the word search puzzle using langflow agentic AI flow.
