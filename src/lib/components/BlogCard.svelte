<script>
    let title = $state("Blog Title")
    let content = $state("Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet.")
    
    const { blog } = $props();
</script>

<div class="card">
    <h2>{blog.title}</h2>
    <p>{blog.content}</p>
</div>

<style>
    h2{
        text-align: center;
    }

    .card {
        background-color: #00B4D8;
        border-radius: 15px;
        min-height: 200px;
        width: 40%;
        padding-left: 20px;
        padding-right: 20px;
        padding-bottom: 20px;
        box-shadow: 0 5px 10px rgba(0, 0, 0, 0.1);
        margin-bottom: 15px;
    }
</style>