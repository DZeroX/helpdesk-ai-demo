<script setup>
    import { ref } from 'vue'
    import { tickets } from '../stores/Tickets.js'
    
    const message = ref(''), answer = ref(''), prompt = ref(''), output = ref('')

    const submit = async () => {
        if (message.value.value == '') {
            message.value.focus()
            return;
        }

        var myHeaders = new Headers();
        myHeaders.append("Authorization", "Bearer " + import.meta.env.VITE_OPENAI_API_KEY);
        myHeaders.append("Content-Type", "application/json");

        var raw = JSON.stringify({
            "model": "gpt-3.5-turbo",
            "messages": [{
                "role": "user",
                "content": `Analyze the following statement: ` + message.value.value + `
I need the following information:
- Category. It has to be one of the following: question, complaint, suggestion, praise, request, unknown.
- Sentiment. It has to be one of the following: positive, neutral, negative.
Format the response as a one-line JSON, all lowercase. where the key cannot exceed one word and the value cannot exceed one word.`
            }]
        });

        prompt.value.value = raw;

        var requestOptions = {
            method: 'POST',
            headers: myHeaders,
            body: raw,
            redirect: 'follow'
        };

        await fetch("https://api.openai.com/v1/chat/completions", requestOptions)
        .then(response => response.json())
        .then(result => {
            output.value.value = JSON.stringify(result)
            var analysis = JSON.parse(result.choices[0].message.content)
            var ticket = { "message": message.value.value, "category": analysis.category, "sentiment": analysis.sentiment }
            tickets.list.push(ticket)
            answer.value.value = "The statement is a \"" + analysis.category + "\". The sentiment is \"" + analysis.sentiment + "\"."
            message.value.placeholder = message.value.value
            message.value.value = ""
            message.value.focus()
        })
        .catch(error => {
            answer.value.value = error.code
        });
    }
</script>

<template>
    <form class="text-center">
        <div class="row">
            <div class="col mb-3 form-group">
                <label for="message" class="form-label">Message</label>
                <input type="text" class="form-control" id="message" ref="message" autofocus />
            </div>
            <div class="col mb-3 form-group">
                <label for="answer" class="form-label">Answer</label>
                <input type="text" class="form-control" id="answer" ref="answer" />
            </div>
        </div>
        <div class="row">
            <div class="col mb-3 form-group">
                <label for="prompt" class="form-label">Prompt</label>
                <textarea type="text" class="form-control" id="prompt" ref="prompt" rows="8"></textarea>
            </div>
            <div class="col mb-3 form-group">
                <label for="output" class="form-label">Output</label>
                <textarea type="text" class="form-control" id="output" ref="output" rows="8"></textarea>
            </div>
        </div>
        <button type="button" class="btn btn-primary" @click="submit">
            <i class="bi bi-send-fill me-2"></i>
            Send
        </button>
    </form>
</template>

<style scoped>
    form {
        margin: 0 auto;
    }
    
    .form-group {
        text-align: left;
    }
</style>