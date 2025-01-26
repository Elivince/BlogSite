<script lang="ts">
    import { createClient } from "@supabase/supabase-js";
    import { PUBLIC_SUPABASE_ANON_KEY, PUBLIC_SUPABASE_URL } from "$env/static/public";
    import { goto } from "$app/navigation";
    import { onMount } from "svelte";
	import ShowBlog from "$lib/components/ShowBlog.svelte";

    const supabase = createClient("https://wztiwulessrpzwbfdkrd.supabase.co", "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Ind6dGl3dWxlc3NycHp3YmZka3JkIiwicm9sZSI6ImFub24iLCJpYXQiOjE3Mzc2OTI3NzMsImV4cCI6MjA1MzI2ODc3M30.KVKASn8AL5tCBvXSRAoqqNFrmFtXDM67RhGSHRDRZrI")

    let blogs: Array<any> = $state([]);
    let selectedBlogId = $state();

    let showBlog = $state(false);

    const toggleShowBlog = (blogId: number) => {
        selectedBlogId = blogId;
        showBlog = !showBlog;
    };

    const fetchBlogs = async () => {
        const { data, error} = await supabase
            .from("blogs")
            .select("*");

        if (!error) {
            blogs = data
        } else {
            alert("Error fetching blogs: " + error.message);
        }
    };

    const goToMainPage = () => {
        goto("/BlogSite");
    };

    onMount(() => {
        fetchBlogs();
    })

</script>

<h1>Latest Tech Blogsite</h1>
<h2>List of Blogs</h2>

<button id="mainPage" onclick={goToMainPage}>
    Main Page
</button>

<div class="listLayout">
        {#each blogs as blog}
            <button id="listBlogs" onclick={() => toggleShowBlog(blog.id)}>
                {blog.title}
            </button>
        {/each}
</div>

{#if showBlog}
    <div class="modal">
        <div class="modal-content">
            <ShowBlog {selectedBlogId} {toggleShowBlog} />
        </div>
    </div>
{/if}

<style>
    h1{
        text-align: center;
        font-size: 3em;
    }

    h2{
        text-align: center;
        font-size: 2.5em;
        margin-top: 0;
    }

    #mainPage {
        border: none;
        background-color: #0077B6;
        border-radius: 5px;
        padding: 10px;
        min-width: 10%;
        margin-bottom: 20px;
        font-family: sans-serif;
        font-weight: bold;
    }

    #mainPage:hover {
        background-color: #0168a0;
    }

    .listLayout{
        display: grid;
        text-align: center;
        grid-template-columns: repeat(4, 1fr);
        width: 60%;
        margin: 50px;
    }

    #listBlogs{
        background-color: transparent;
        border: none;
        margin-bottom: 5px;
        border-radius: 20px;
    }

    #listBlogs:hover {
        background-color: #ADE4F1;
    }

    .modal {
        display: flex;
        align-items: center;
        justify-content: center;
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 80%;
    }

    .modal-content {
        display: flex;
        align-items: center;
        justify-content: center;
        background: rgba(255, 255, 255, 0.262);
        width: 100%;
        height: 100%;
    }
</style>