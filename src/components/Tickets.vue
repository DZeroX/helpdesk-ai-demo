<script setup>
    import { tickets } from '../stores/Tickets';
</script>

<template>
    <table class="table table-bordered">
        <thead class="table-dark">
            <tr>
                <td>ID</td>
                <td>Message</td>
                <td>Category</td>
                <td>Sentiment</td>
            </tr>
        </thead>
        <tbody>
            <tr v-for="(ticket, index) in tickets.list">
                <td>{{ index }}</td>
                <td>{{ ticket.message }}</td>
                <td>{{ ticket.category }}</td>
                <td>{{ ticket.sentiment }}</td>
            </tr>
        </tbody>
    </table>
</template>

<style scoped>
</style>