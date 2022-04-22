<script>
export default {
    props: { results: {} }
}
</script>

<template>
    <table>
        <tr v-for="(status, name) in results" :key="name">
            <td>{{name}}</td>
            <td>{{status ? 'valid' : 'invalid' }}</td>
        </tr>
    </table>
</template>