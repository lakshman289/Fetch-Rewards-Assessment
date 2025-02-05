Receipt Processor Challenge
This repository holds my source code for a take-home exam for Fetch Rewards, this assignment runs a server using Go and adds to endpoints to this server to:

Send a Receipt JSON object to the /receipts/process POST endpoint which parses the receipt object and pools up points given a point ruleset via the assignment instructions
This endpoint returns a JSON object containing the ID of the receipt that was submitted which is used to reference later in the GET endpoint.
Errors are also handled for missing attributes in the POST's JSON body, errors parsing the date and time attributes, and other internal server errors that may occur.
Fetch the points associated with a submitted Receipt from the /receipts/{id}/points endpoint using the id returned from hitting the POST endpoint above. This endpoint returns a JSON object that contains the total pooled points that the receipt submitted earlier accrued.
Errors are also handled here, including if the id sent via the endpoint's path doesn't match an entry in the in-memory store/map of receipts
Note:

The code that's directly related to this assignment can be found in: receipt-api.go, /receiptstructs/structs.go, and /receiptstructs/methods.go
The frontend aspect of this assignment is strictly for testing the Go backend endpoints
Instructions below go into detail on how to test the Go server endpoints using both curl and the frontend
How to run the Receipt API server using Docker
Within the fetch-receipt-processor-challenge directory build the Docker image by running docker build --tag fetch-receipt-api .

Confirm that a Docker image was created by running this command from the same directory docker image ls

If you see a new entry in the table printed with the name fetch-receipt-api and the tag latest then the docker build should have worked, you should see something like this printed in your console:

fetch-receipt-api || latest || 27a893d09711 || 6 seconds ago || 851MB

Run the docker image using this command from the same directory docker run -d -p 8000:8000 fetch-receipt-api

This will start the Docker image connecting the Docker container's port 8000 to your machine's 8000 port, this also allows the image to run in the background freeing up your console instead of forcing you to open a new one.
You do not need to specify the host here, although if you prefer to specify localhost or 127.0.0.1 that will still work.
Verify that you see the Docker container running in the Docker Desktop app

Docker/Go Notes:

This assumes you already have docker installed and know how to use it/know the basics around Docker and also have Go installed and configured
For instructions on how to install and configure Go using Windows Subsystem for Linux (WSL) this link proved very helpful to myself
How to run the Receipt API server without Docker
Within the fetch-receipt-processor-challenge directory simply run this command go run receipt-api.go
Go Notes:

This assumed you have Go installed and configured on your machine
For instructions on how to install and configure Go using Windows Subsystem for Linux (WSL)
How to run the Receipt API frontend
From within the receipt-frontend directory run yarn install
Run yarn start in the same receipt-frontend directory
Navigate to localhost:3000
Note:

The frontend relies on the Go backend server, so make sure before using the frontend you build and run the backend Docker container
How to test the Receipt API
Using curl
From your console run this curl command to hit the /receipts/process POST endpoint:
This command specifically holds the same Receipt data used in example one of the assignment
Note: This command uses the verbose option so you can see everything sent back
curl -v -d '{"retailer": "Target", "purchaseDate": "2022-01-01", "purchaseTime": "13:01", "items": [{"shortDescription": "Mountain Dew 12PK", "price": "6.49"}, {"shortDescription": "Emils Cheese Pizza", "price": "12.25"}, {"shortDescription": "Knorr Creamy Chicken", "price": "1.26"}, {"shortDescription": "Doritos Nacho Cheese", "price": "3.35"}, {"shortDescription": " Klarbrunn 12-PK 12 FL OZ ", "price": "12.00"}], "total": "35.35"}' -X POST 'http://localhost:8000/receipts/process'

Verify you are sent back a JSON object that follows this format below:
{ "id" : "1c23395b-7b6e-47bf-887c-f8e7608c809c" }

Copy the uuid, this will be used to hit the /receipts/{id}/points GET endpoint

From the console run this curl command to hit the /receipts/{id}/points endpoint using the id received from the previous POST request:

Note: The uuid between receipts and points in this path must be the id from earlier
curl -v -X GET 'http://localhost:8000/receipts/1c23395b-7b6e-47bf-887c-f8e7608c809c/points'

Verify you are sent back a JSON object that follows this format below:
{ "points" : "28" }

In this case 28 should be the correct amount of points that the receipt sent earlier has
Further testing
I will include another POST curl command that represents example two from the assignment, this receipt should accrue 109 points -- so verify this is what you see after sending your GET request.

curl -v -d '{"retailer": "M&M Corner Market", "purchaseDate": "2022-03-20", "purchaseTime": "14:33", "items": [{"shortDescription": "Gatorade", "price": "2.25"}, {"shortDescription": "Gatorade", "price": "2.25"}, {"shortDescription": "Gatorade", "price": "2.25"}, {"shortDescription": "Gatorade", "price": "2.25"}], "total": "9.00"}' -X POST 'http://127.0.0.1:8000/receipts/process'

Use the same GET curl command from the above section using the new id -- verify that 109 points are returned.

Using the frontend
After running the backend Docker container and starting the frontend fill out the fields to create a Receipt object
You will see error messages if a field is missing both in the main form and in the add receipt items section
Verify that the total field towards the bottom is correct based off of the items entered above
Click the Submit Receipt button to POST the receipt object to the backend
You will see a success message towards the top of the form if the POST request was successful
Click the View Receipt Points button to view the accrued points for the submitted receipt
