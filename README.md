const express = require('express');
const bodyParser = require('body-parser');
const axios = require('axios');
const app = express();

app.use(bodyParser.json());

// Endpoint for Dialogflow webhook
app.post('/webhook', async (req, res) => {
    const intent = req.body.queryResult.intent.displayName;

    // Example for Order Status Intent
    if (intent === 'OrderStatus') {
        const orderNumber = req.body.queryResult.parameters['order_number'];
        
        // Here, call your backend API to fetch order status (replace with your API)
        try {
            const orderStatus = await axios.get(`https://api.example.com/order/${orderNumber}`);
            return res.json({
                fulfillmentText: `Your order status is: ${orderStatus.data.status}`
            });
        } catch (error) {
            return res.json({
                fulfillmentText: 'I’m sorry, I couldn’t fetch the order status. Please try again later.'
            });
        }
    }

    // Example for Prescription Refill Intent
    if (intent === 'PrescriptionRefill') {
        const prescriptionId = req.body.queryResult.parameters['prescription_id'];
        
        // Call your API to process prescription refill
        try {
            const refillStatus = await axios.post('https://api.example.com/refill', { prescriptionId });
            return res.json({
                fulfillmentText: `Your prescription refill is being processed. You will receive a notification once it is ready.`
            });
        } catch (error) {
            return res.json({
                fulfillmentText: 'I’m sorry, there was an issue with your prescription refill request. Please try again later.'
            });
        }
    }

    res.sendStatus(200);
});

// Start the server
app.listen(5000, () => {
    console.log('Server is running on port 5000');
});
