<script>
    import { onMount } from "svelte";
    import Navbar from "./Home Components/Navbar.svelte";
    import Blogs from "./Home Components/Blogs.svelte";    
    import { getAllblogs, newPost } from "../service/allapi";
    import Recomended from "./Home Components/Recomended.svelte";
    import Createarea from "./Home Components/Createarea.svelte";

    let allBlogs = [];

    const getallTales = async () => {
        const response = await getAllblogs();
        allBlogs = response.data;
    };

    const handleBlogSubmit = (blogContent) => {
        newBlog(blogContent);
    };

    const newBlog = async (blogContent) => {
        const response = await newPost(blogContent);
        getallTales();
    };

    onMount(() => {
        getallTales();
    });
</script>

<div class="unregistered-home-contents">
    <div class="blogs-display">
        <Navbar />
        <Createarea onBlogSubmit={handleBlogSubmit} />
        <hr class="mb-5" />
        <Blogs blogs={allBlogs} />
    </div>
    <div class="divider" />
    <div class="recomended">
        <Recomended recBlogs={allBlogs} />
    </div>
</div>
