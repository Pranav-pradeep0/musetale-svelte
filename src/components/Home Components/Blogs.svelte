<script>
    import { onMount } from 'svelte';
    import { BASE_URL } from '../../service/baseurl';
  
    let blogs = [];
    let maxContentLength = 500;
    let blogsPerPage = 10;
    let currentPage = 0;
  
    const truncateContent = (content) => {
      if (content.length <= maxContentLength) {
        return content;
      }
      return content.slice(0, maxContentLength) + '...';
    };
  
    let truncatedBlogs = [];
  
    let pageCount = 0;
    let offset = 0;
  
    let currentBlogs = [];
  
    let handlePageClick = (data) => {
      currentPage = data.selected;
      updateCurrentBlogs();
    };
  
    function updateCurrentBlogs() {
      offset = currentPage * blogsPerPage;
      currentBlogs = truncatedBlogs.slice(offset, offset + blogsPerPage);
    }
  
    updateCurrentBlogs();
  
    onMount(() => {
      truncatedBlogs = blogs.map((blog) => ({
        ...blog,
        content: truncateContent(blog.content),
      }));
      pageCount = Math.ceil(truncatedBlogs.length / blogsPerPage);
      updateCurrentBlogs();
    });
  </script>
  
  {#if blogs.length > 0}
    <div>
      <h1 class="pb-5 ps-4">Tales</h1>
      <div class="d-flex flex-column gap-5 mb-5">
        {#each currentBlogs as blog, index}
          <div class="card blog-card bg-dark" key={index}>
            <div class="card-header d-flex justify-content-between">
              <div class="d-grid">
                <h4>{blog.title}</h4>
                <small class="text-muted">Written by : {blog.author.username}</small>
              </div>
              <ul class="navbar-nav flex-row">
                <li class="nav-item me-3 me-lg-1">
                  <span class="nav-link d-sm-flex align-items-sm-center">
                    <img src={`${BASE_URL}/uploads/${blog.author.profilePic}`} class="rounded-circle" height="40" loading="lazy" alt=""/>
                  </span>
                </li>
              </ul>
            </div>
            <div class="card-body">
              <p class="card-text">{truncateContent(blog.content)}</p>
              <div class="d-flex">
                <button class="btn btn-primary ms-auto" type="button" data-bs-toggle="offcanvas" data-bs-target={`#offcanvasBottom-${index}`} aria-controls={`offcanvasBottom-${index}`}>Read More</button>
              </div>
            </div>
            <div class="offcanvas offcanvas-bottom bg-dark" data-bs-scroll="true" tabindex="-1" id={`offcanvasBottom-${index}`} aria-labelledby={`offcanvasBottomLabel-${index}`}>
              <div class="offcanvas-header mx-4 mt-5">
                <div class="ms-2">
                  <h2 class="offcanvas-title text-uppercase" id="offcanvasBottomLabel">{blog.title}</h2>
                  <span class="small disabled mx-1 my-2">By {blog.author.name} <p>On {blog.date}</p></span>
                </div>
                <button type="button" class="btn-close" data-bs-dismiss="offcanvas" aria-label="Close"></button>
              </div>
              <hr class="m-0" />
              <div class="offcanvas-body px-5 py-4">
                {blog.content}
              </div>
            </div>
          </div>
        {/each}
      </div>
      <!-- <div class="d-flex mx-auto justify-content-center">
        <ReactPaginate
          previousLabel={'Previous'}
          nextLabel={'Next'}
          breakLabel={'...'}
          pageCount={pageCount}
          marginPagesDisplayed={2}
          pageRangeDisplayed={5}
          onPageChange={handlePageClick}
          containerClassName={'pagination'}
          subContainerClassName={'pages pagination'}
          activeClassName={'active'}
        />
      </div> -->
    </div>
  {:else}
    <div>
      <h1 class="pb-5 ps-4">Tales</h1>
      <div class="d-flex flex-column blog-card gap-5 mb-5">
        <h5 class="text-center">No blogs found.</h5>
      </div>
    </div>
  {/if}
  