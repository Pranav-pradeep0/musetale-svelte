<script>
    import { onMount } from 'svelte';
  
    let recBlogs = []; // Assuming recBlogs is a prop
  
    function shuffleArray(array) {
      let shuffledArray = [...array];
      for (let i = shuffledArray.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [shuffledArray[i], shuffledArray[j]] = [shuffledArray[j], shuffledArray[i]];
      }
      return shuffledArray;
    }
  
    const maxContentLength = 70;
  
    function truncateContent(content) {
      if (content.length <= maxContentLength) {
        return content;
      }
      return content.slice(0, maxContentLength) + '...';
    }
  
    function getFirstFiveBlogs(blogs) {
      return blogs.slice(0, 5);
    }
  
    let firstFiveBlogs = [];
  
    onMount(() => {
      const shuffledBlogs = shuffleArray(recBlogs);
      firstFiveBlogs = getFirstFiveBlogs(shuffledBlogs);
    });
  </script>
  
  <div class="d-flex flex-column gap-4">
    <h5 class="py-3">Recommended for you ...</h5>
    {#each firstFiveBlogs as blog (blog.id)}
      <div class="card recomendation-card" style="width: 18rem;">
        <div class="card-body">
          <div class="d-grid">
            <h5 class="card-title ms-auto">{blog.title}</h5>
            <h6 class="card-subtitle mb-2 ms-auto text-muted">
              {new Date(blog.date).toLocaleString('en-US', { month: 'long', day: 'numeric', year: 'numeric', hour: 'numeric', minute: 'numeric' })}
            </h6>
          </div>
          <hr class="m-0 pb-3" />
          <p class="card-text">{truncateContent(blog.content)}</p>
          <div class="d-flex">
            <p class="card-link ms-auto">Read More</p>
          </div>
        </div>
      </div>
    {/each}
  </div>
  