require('dotenv').config();
const { Client, GatewayIntentBits } = require('discord.js');
const axios = require('axios');

const client = new Client({
  intents: [
    GatewayIntentBits.Guilds,
    GatewayIntentBits.GuildMessages,
    GatewayIntentBits.MessageContent
  ]
});

client.once('ready', () => {
  console.log(Logged in as ${client.user.tag});
});

client.on('messageCreate', async (message) => {
  if (message.author.bot) return;

  try {
    const res = await axios.post(
      "https://api.openai.com/v1/chat/completions",
      {
        model: "gpt-4o-mini",
        messages: [{ role: "user", content: message.content }]
      },
      {
        headers: {
          Authorization: Bearer ${process.env.AI_API_KEY},
          "Content-Type": "application/json"
        }
      }
    );

    message.reply(res.data.choices[0].message.content);

  } catch (err) {
    console.log(err);
    message.reply("Error contacting AI.");
  }
});

client.login(process.env.DISCORD_TOKEN);
