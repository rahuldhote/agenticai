**AWS Bedrock Agent — Gift Recommender**

**Goal:**
  Build an ecommerce chatbot recommending the products for various occasion like birthday,personal events adding it in cart & recommend the personalized gift wrapping option to end user.

  *Technology Stack*: AWS Services like IAM Roles, Cloudformation, Bedrock, Lambda, Dynamo DB & S3.

**High Level Design**

![alt text](design.png)

Components:

User (Chat Interface)
The user interacts via a natural language conversation chat interface
The user describes the gift recipient & occasion
The agent headers/result are shown to the user (e.g., gift score ID)
There is a feedback loop: Claude analyzes gift descriptions and returns recommendations back to the user.


Amazon Bedrock Agent — product-recommendation-agent
Model: Claude Sonnet 4.5 | IAM Role: productallowsaj-ws4HRwi3IIAmazonBedrock


Action Groups (API Schema Definitions)
There are 4 API actions defined:

Action	Method	Required Params	Returns
get-product-recommendations	GET /products	Optional: person, category, gender, occasion, product_name	JSON array with descriptions

get-cart	GET /cart	Required: person (user_id)	Array of product_name strings in user's cart
add-item-to-cart	POST /cart	Required: person, product_name	HTTP 200 on success / HTTP 409 if already in cart

get-amazon-personalize-recommendation	GET /personalize	Required: person, product_name	Returns 1 recommended product with name, description, category, gender, occasion


AWS Lambda Functions
Four Lambda functions back the above APIs:

1. GetProductDetailsFunction
Scans Products DynamoDB table
Builds FilterExpression from: category, gender, occasion
Params passed by the agent
Returns matching products as JSON array with descriptions

2. GetCartFunction
Queries Cart DynamoDB table
Uses user_id as key
Returns JSON array of product_name strings representing all items in the user's cart

3. AddToCartFunction
Writes new item to Cart Table
Records: (userID, productName)
Checks for duplicate entry
Returns HTTP 200 on success
Returns HTTP 409 if product already in cart


4. GetPersonalizeRecommendation Function
Schedules Amazon Personalize
"Customers also bought" pattern
Input: product_name string

Output: 1 recommended product with name, description, category, gender, occasion

Supporting: PopulateProductsTableFunction
One-time setup function
Generates/populates 100 sample gift products into the DynamoDB Products table
Runs once for workshop setup

Data Stores & AWS Services

Amazon DynamoDB — Products Table
Table: productTable[env]-ws-Products-XXXX
Partition Key: product_name (String)
Attributes: category | gender | occasion
Additional: product_description (String)

GSI on: gender (efficient gender filter)
GSI on: occasion (efficient occasion filter)

Pre-loaded: 100 sample gift products

Amazon DynamoDB — Cart Table

Table: productTable[env]-ws-Cart-XXXXX
Attribute: user_id (String) = user's email
Attribute: product_name (String)
Composite key: user_id + product_name

Stores all items added to user's cart
Persists across entire conversation


Knowledge Base for Amazon Bedrock — RAG Pipeline (Gift Wrapping Ideas)

Amazon S3 (Data Source)
Bucket: amazonwsproductsbucket-#####Meta.txt.txt
File: GiftWrapping.txt
Content: gift wrapping ideas by:
Occasion (Birthday, Anniversary, Wedding…)
Gender (Male, Female, Others)

Product category & type

Step-by-step wrapping instructions
Multiple suggestions & color themes
Privacy: company data secure (not public)

GiftWrappingKnowledgeBase
Service: Knowledge Base for Amazon Bedrock

Data Source: GiftWrappingKBsource
A connector: must manually click "Sync"
Verify type from sync history entries
Fully managed RAG workflow
Auto document chunking & splitting
Auto embedding generation (Titan Embeddings)
Auto vector store provisioning

CloudFormation Stack: Bedrock KBS

Managed Vector Store
Auto-provisioned by Bedrock KB service
Stores embeddings of GiftWrapping.txt
Performs semantic similarity search

When agent queries for wrapping ideas → matches query context to relevant passages in the source document

KB Instructions for Agent
Added in Agent Builder → Knowledge Bases section

Instruction text: "This is a gift wrapping knowledge base for ideas on how to wrap gifts based on the gift details, the occasion, and its recipient"

We can configure the resources required for chatbot manually on AWS or can create the cloud formation template to configure all the resource required.

Attached Cloudformation Template Design

CloudFormation Auto-Provisions (Bottom Bar)
The entire stack is auto-provisioned via CloudFormation:

DynamoDB Tables (Products + Cart)
Lambda Functions (3 total: GetProducts, GetCart, AddToCart, GetPersonalizeRec, PopulateProducts)
IAM Role
S3 Bucket
GiftWrappingKnowledgeBase


Agent Instructions:

Always start by fetching the full product list from the API to learn valid filter values

Ask the user about gender / category / occasion (skip gender if already obvious from context)

Ask for the user's email — use it as user ID in all API calls throughout the conversation

Refresh adding step: add item to cart, call MakeAGiftAPI to suggest co-purchased items

After each cart addition: auto-fetch and display the updated cart contents to the user

At end of conversation: offer gift wrapping ideas from Knowledge Base | Never expose ref details