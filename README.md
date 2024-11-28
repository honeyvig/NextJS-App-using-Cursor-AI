# NextJS-App-using-Cursor-AI
- I have built a NextJS app using Cursor AI. So far I have the basic UI and login features coded as well as API keys set up

- I need help with the most core feature which will incorporate an open ai output based on a submitted form.

- I am looking to pay on a project completion basis with opportunity to continue working on the project long term

- this project is sports based and made for athletes so if you have interest/passion in this subject, you would be a GREAT FIT!
==============
Here’s how you can integrate OpenAI’s API into your Next.js app to generate outputs based on a submitted form. This code assumes you’ve set up basic authentication and API key management in your project. Below is an example implementation:
Steps to Implement the Core Feature

    Set Up the Form: Add a form in your Next.js frontend to capture user input.
    Create API Route: Use Next.js API routes to handle form submissions and interact with OpenAI's API.
    Display the Output: Show the OpenAI response in the frontend dynamically.

Frontend: The Form

Add a page component with a form to collect user input.

import { useState } from "react";

export default function AthleteForm() {
  const [input, setInput] = useState("");
  const [response, setResponse] = useState("");
  const [loading, setLoading] = useState(false);

  const handleSubmit = async (e) => {
    e.preventDefault();
    setLoading(true);

    try {
      const res = await fetch("/api/openai", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ input }),
      });

      if (!res.ok) {
        throw new Error("Error generating response");
      }

      const data = await res.json();
      setResponse(data.output);
    } catch (error) {
      console.error(error);
      setResponse("Failed to fetch response. Try again later.");
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="container">
      <h1>Sports Insights Generator</h1>
      <form onSubmit={handleSubmit}>
        <textarea
          value={input}
          onChange={(e) => setInput(e.target.value)}
          placeholder="Enter your query about training, performance, etc."
          rows={5}
          required
        />
        <button type="submit" disabled={loading}>
          {loading ? "Loading..." : "Generate"}
        </button>
      </form>
      {response && (
        <div className="response">
          <h2>AI Output:</h2>
          <p>{response}</p>
        </div>
      )}
    </div>
  );
}

Backend: API Route

Create a new API route in pages/api/openai.js.

import { Configuration, OpenAIApi } from "openai";

const configuration = new Configuration({
  apiKey: process.env.OPENAI_API_KEY,
});

const openai = new OpenAIApi(configuration);

export default async function handler(req, res) {
  if (req.method !== "POST") {
    res.status(405).send({ message: "Only POST requests are allowed" });
    return;
  }

  const { input } = req.body;

  if (!input) {
    res.status(400).send({ message: "Input is required" });
    return;
  }

  try {
    const aiResponse = await openai.createCompletion({
      model: "text-davinci-003", // Or another model you prefer
      prompt: `You are an expert sports coach. Respond to the following query:\n${input}`,
      max_tokens: 150,
      temperature: 0.7,
    });

    const output = aiResponse.data.choices[0].text.trim();
    res.status(200).json({ output });
  } catch (error) {
    console.error("OpenAI API error:", error);
    res.status(500).send({ message: "Failed to fetch response from OpenAI" });
  }
}

Environment Variables

Add your OpenAI API key to a .env.local file at the root of your Next.js project:

OPENAI_API_KEY=your_openai_api_key_here

Restart your development server to load the new environment variables.
Styling (Optional)

Add some CSS to make the form more user-friendly. Include this in your component or global styles:

.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  text-align: center;
}

textarea {
  width: 100%;
  margin-bottom: 10px;
  padding: 10px;
  font-size: 16px;
}

button {
  padding: 10px 20px;
  background-color: #0070f3;
  color: #fff;
  border: none;
  cursor: pointer;
}

button:disabled {
  background-color: #999;
  cursor: not-allowed;
}

.response {
  margin-top: 20px;
  text-align: left;
}

Key Features of this Implementation

    Frontend Form: Collects athlete-related queries.
    API Integration: Uses openai.createCompletion for intelligent response generation.
    Real-Time Output: Displays the AI-generated output immediately.
    Error Handling: Manages errors gracefully both on the frontend and backend.

Next Steps

    Authentication: Secure the API route with user authentication.
    Database Integration: Save user queries and AI responses for future analysis.
    Custom AI Model: Fine-tune an OpenAI model to provide more tailored responses for athletes.
    Expand Features: Include multimedia content generation or training plans.

This setup should give you a strong foundation to build the core feature of your app. 
