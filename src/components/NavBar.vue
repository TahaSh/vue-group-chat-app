<template>
<div>
    <nav>
        <div class="float-left">
            <img src="../assets/logo.svg">&nbsp;
            <span id="title">Chat</span>
        </div>
        <div class="float-right">
            <span>Welcome <b>{{ name }}</b> </span>&nbsp;
            <img src="../assets/profile-lady.svg">
        </div>
    </nav>
</div>
</template>

<script>
export default {
  props: ["name"]
};
</script>
