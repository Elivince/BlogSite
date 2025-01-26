<script lang="ts">
    import { onMount } from "svelte";
    import { createClient } from "@supabase/supabase-js";
    import { PUBLIC_SUPABASE_ANON_KEY, PUBLIC_SUPABASE_URL } from "$env/static/public";

    const supabase = createClient("https://wztiwulessrpzwbfdkrd.supabase.co", "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Ind6dGl3dWxlc3NycHp3YmZka3JkIiwicm9sZSI6ImFub24iLCJpYXQiOjE3Mzc2OTI3NzMsImV4cCI6MjA1MzI2ODc3M30.KVKASn8AL5tCBvXSRAoqqNFrmFtXDM67RhGSHRDRZrI")

    let blogs: any = $state([]);
    
    const { selectedBlogId, toggleShowBlog } = $props();

    const fetchBlogs = async () => {

        const { data, error} = await supabase
            .from("blogs")
            .select("*")
            .eq("id", selectedBlogId)
            .single();

        if (!error) {
            blogs = data;
        } else {
            alert("Error fetching blogs: " + error.message);
        }
    };

    onMount(() => {
        fetchBlogs();
    });
</script>

<div class="card">
    <div class="card-header">
        <button id="close" onclick={toggleShowBlog}>X</button>
    </div>
    <h2>{blogs.title}</h2>
    <p>{blogs.content}</p>
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
        position: relative;
    }

    .card-header {
        display: flex;
        justify-content: flex-end;
    }

    #close {
        border: none;
        background-color: #1a8dcb9f;
        border-radius: 50px;
        font-weight: bold;
        font-size: 20px;
        padding: 5px;
        padding-left: 10px;
        padding-right: 10px;
        margin-top: 20px;
    }

    #close:hover {
        background-color: #1a83bc;
    }
</style>