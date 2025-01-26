<script lang="ts">
    let userTitle = $state("")
    let userContent = $state("")

    const tester = async () => {
        const entries: Array<any> = [];

        for(let i = 1; i < 51; i++) {

            entries.push({
                title: userTitle + " " + i,
                content: userContent,
            });
        }
        
        const {} = await supabase.from("blogs").insert(entries);

        userTitle = "";
        userContent = "";
        toggleAddBlog();
    }

    const { toggleAddBlog, supabase } = $props();

    const saveBlogPost = async () => {
        if (!userTitle || !userContent) {
            alert("Please fill in both the title and content");
            return;
        }

        const { data, error } = await supabase
            .from("blogs")
            .insert([
                {
                    title: userTitle,
                    content: userContent
                }
            ]);

        if (error) {
            alert("Error inserting blog post: " + error.message);
        } else {
            toggleAddBlog();
            userTitle=""
            userContent=""
            alert("Blog posted!");
        }

        return;
    };


</script>

<div class="card">
    <input
        id="title"
        bind:value={userTitle}
        placeholder="Title of your blog"
    />
        <button id="close" onclick={toggleAddBlog}>X</button>
        <div class="contentContainer">
            <textarea
                id="content"
                bind:value={userContent}
                placeholder="What is in your blog?"
            ></textarea>
        </div>
    <button id="post" onclick={saveBlogPost}>Post</button>
</div>

<style>
    #title, #content{
        padding: 10px;
        margin: 5px;
        border-radius: 5px;
        border: none;
        background-color: #ffffff51;
    }

    #title:focus, #content:focus {
        outline: none;
    }

    #title {
        width: 50%;
        text-align: center;
        font-size: 24px;
        font-weight: bold;
    }
    
    #content {
        width: 100%;
        font-size: 16px;
        line-height: 1.5;
        overflow-y: auto;
        resize: none;
    }

    .card {
        display: flex;
        align-items: center;
        flex-direction: column;
        background-color: #48d7f4;
        border-radius: 15px;
        min-height: 80%;
        width: 50%;
        padding: 20px;
        position: relative;
    }

    .contentContainer {
        padding-top: 10px;
        width: 90%;
        flex-grow: 1;
        display: flex;
    }

    #post {
        border: none;
        background-color: #1a8dcb;
        border-radius: 5px;
        padding: 10px;
        min-width: 20%;
        font-family: sans-serif;
        font-weight: bold;
        margin-top: 20px;
    }

    #post:hover, #close:hover {
        background-color: #1a83bc;
    }

    #close {
        position: absolute;
        top: 30px;
        right: 30px;
        border: none;
        background-color: #1a8dcb9f;
        border-radius: 50px;
        font-weight: bold;
        font-size: 20px;
        padding: 5px;
        padding-left: 10px;
        padding-right: 10px;
    }

</style>