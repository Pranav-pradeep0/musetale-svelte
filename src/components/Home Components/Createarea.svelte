<script>
    import { onMount } from 'svelte';
  
    let blogContent = {
      title: '',
      content: '',
      authorid: '',
    };
  
    let responseUserObjectparsed;
  
    onMount(() => {
      responseUserObjectparsed = JSON.parse(localStorage.getItem('user'));
    });
  
    function handleInputChange(event) {
      const { name, value } = event.target;
  
      blogContent = {
        ...blogContent,
        [name]: value,
        authorid: responseUserObjectparsed._id,
      };
    }
  
    function handlePost() {
      onBlogSubmit(blogContent);
      blogContent = {
        title: '',
        content: '',
        authorid: '',
      };
    }
  </script>
  
  <div class="mx-3">
    {#if responseUserObjectparsed}
      <div class="card create-card bg-dark mx-auto my-5">
        <div class="card-body">
          <h5 class="card-title ps-4 pt-4 pb-3 text-center">Write a blog</h5>
          <p class="card-text">
            <input
              class="form-control"
              placeholder="Title"
              type="text"
              bind:value={blogContent.title}
              name="title"
              on:input={handleInputChange}
            />
          </p>
          <p class="card-text">
            <textarea
              class="form-control"
              placeholder="...."
              bind:value={blogContent.content}
              name="content"
              on:input={handleInputChange}
            />
          </p>
          <div class="d-flex">
            <button class="btn btn-outline-primary ms-auto me-4" on:click={handlePost}>Post</button>
          </div>
        </div>
      </div>
    {/if}
    {#if !responseUserObjectparsed}
      <div class="card create-card bg-dark mx-auto my-5">
        <div class="card-body">
          <h5 class="card-title ps-4 pt-4 pb-3">Write and Inspire</h5>
          <p class="card-text ps-4">Start a blog on musetale right away, start now with just one click</p>
          <div class="d-flex">
            <a href="/login" class="btn btn-outline-primary ms-auto me-4">Login Or Signup to Get Started</a>
          </div>
        </div>
      </div>
    {/if}
  </div>
  