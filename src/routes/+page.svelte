<script lang="ts">
    import { onMount } from "svelte";
    import { PUBLIC_SUPABASE_ANON_KEY, PUBLIC_SUPABASE_URL } from "$env/static/public";
    import { createClient } from "@supabase/supabase-js";
    import AddBlog from "$lib/components/AddBlog.svelte";
    import BlogCard from "$lib/components/BlogCard.svelte";
    import { goto } from "$app/navigation";

    const supabase = createClient("https://wztiwulessrpzwbfdkrd.supabase.co", "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Ind6dGl3dWxlc3NycHp3YmZka3JkIiwicm9sZSI6ImFub24iLCJpYXQiOjE3Mzc2OTI3NzMsImV4cCI6MjA1MzI2ODc3M30.KVKASn8AL5tCBvXSRAoqqNFrmFtXDM67RhGSHRDRZrI")

    let showAddBlog = $state(false);

    let blogs: Array<any> = $state([]);
    let blogsCount = $state(0);
    let currentPage = $state(1);
    const pageSize = 10;

    const toggleAddBlog = () => {
        showAddBlog = !showAddBlog;
    };

    onMount(() => {
        const listener = supabase
            .channel('blogs')
            .on('postgres_changes', { event: 'INSERT', schema: 'public', table: 'blogs' },
            (payload) => {
                blogs = [...blogs, payload.new];
                }    
            )
            .subscribe()

            return () => {
                supabase.removeChannel(listener);
            }
    });

    const fetchBlogs = async () => {
        const start = (currentPage - 1) * pageSize;
        const end = start + pageSize - 1;

        const { data, error, count } = await supabase
            .from("blogs")
            .select("*", { count: "exact" })
            .range(start, end);

        if (!error) {
            blogs = data;
            blogsCount = count || 0;
        } else {
            alert("Error fetching blogs: " + error.message);
        }
    };

    onMount(() => {
        fetchBlogs();
    });

    const changePage = (page: number) => {
        currentPage = page;
        fetchBlogs();
    };

    const goToListPage = () => {
        goto("/BlogSite/list");
    };
</script>

<h1>Latest Tech Blogsite</h1>

<button id="addButton" onclick={toggleAddBlog}>
    Add
</button>

<button id="listPage" onclick={goToListPage}>
    List Page
</button>

<div class="paginationRow">
    {#if blogsCount > 0}
        <p>Pages: </p>
    {/if}
    {#each Array(Math.ceil(blogsCount / pageSize)) as _, i}
        <button id="navButton" class:active={i + 1 === currentPage}
            onclick={() => changePage(i + 1)}>
            {i + 1}
        </button>
    {/each}
</div>

{#if blogsCount > 0}
    {#each blogs as blog}
        <BlogCard {blog}/>
    {/each}
{/if}

{#if showAddBlog}
    <div class="modal">
        <div class="modal-content">
            <AddBlog {toggleAddBlog} {supabase} />
        </div>
    </div>
{/if}

<div class="paginationRow">
    {#if blogsCount > 0}
        <p>Pages: </p>
    {/if}
    {#each Array(Math.ceil(blogsCount / pageSize)) as _, i}
        <button id="navButton" class:active={i + 1 === currentPage}
            onclick={() => changePage(i + 1)}>
            {i + 1}
        </button>
    {/each}
</div>

<style>
    h1{
        text-align: center;
        font-size: 3em;
    }

    #addButton, #listPage {
        border: none;
        background-color: #0077B6;
        border-radius: 5px;
        padding: 10px;
        min-width: 10%;
        margin-bottom: 20px;
        font-family: sans-serif;
        font-weight: bold;
    }

    #addButton:hover, #listPage:hover {
        background-color: #0168a0;
    }

    .modal {
        display: flex;
        align-items: center;
        justify-content: center;
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
    }

    .modal-content {
        display: flex;
        align-items: center;
        justify-content: center;
        background: rgba(255, 255, 255, 0.262);
        width: 100%;
        height: 100%;
    }

    #navButton{
        background-color: transparent;
        max-width: 20px;
        border: none;
    }

    .paginationRow {
        display: flex;
        justify-content: center;
        align-items: center;
        gap: 5px;
        width: 100%;
        margin-bottom: 50px;
    }
</style>