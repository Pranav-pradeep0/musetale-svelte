<script>
    import { onMount } from "svelte";
    import { userPosts, editpost, deletePost } from "../../service/allapi";
  
    let currentUSerPosts = [];
  
    let editInfo = {
      postId: null,
      editedTitle: "",
      editedContent: "",
    };
  
    let currentUser = JSON.parse(localStorage.getItem("user"));
  
    async function getUserPosts() {
      const response = await userPosts(currentUser._id);
      currentUSerPosts = response.data;
    }
  
    function handleEdit(postId, title, content) {
      editInfo = { postId, editedTitle: title, editedContent: content };
    }
  
    async function handleSaveEdit() {
      const { postId, editedTitle, editedContent } = editInfo;
  
      const response = await editpost(postId, {
        title: editedTitle,
        content: editedContent,
      });
  
      console.log(response);
  
      if (response.status === 200) {
        getUserPosts();
        editInfo = { postId: null, editedTitle: "", editedContent: "" };
      }
    }
  
    async function handlePostDelete(postId) {
      const response = await deletePost(postId);
  
      if (response.status === 200) {
        getUserPosts();
      }
    }
  
    onMount(() => {
      getUserPosts();
    });
  </script>
  
  <div>
    <div>
      <h2 class="text-center mb-5">Your Blogs</h2>
    </div>
  
    {#if currentUSerPosts.length === 0}
      <div>
        <h3>No Posts to show</h3>
      </div>
    {/if}
  
    {#each currentUSerPosts as post (post._id)}
      <div class="card blog-card bg-dark mb-5" key={post._id}>
        <div class="card-header d-grid">
          <div>
            {#if editInfo.postId === post._id}
              <input
                class="form-control title-edit-input"
                bind:value={editInfo.editedTitle}
                on:input={(e) =>
                  (editInfo = {
                    postId: post._id,
                    editedTitle: e.target.value,
                    editedContent: editInfo.editedContent,
                  })
                }
              />
            {:else}
              <h4>{post.title}</h4>
            {/if}
          </div>
          <div class="">
            <small class="text-muted">{post.date}</small>
          </div>
        </div>
        <div class="card-body">
          {#if editInfo.postId === post._id}
            <textarea
              class="form-control edit-textarea"
              bind:value={editInfo.editedContent}
              on:input={(e) =>
                (editInfo = {
                  postId: post._id,
                  editedTitle: editInfo.editedTitle,
                  editedContent: e.target.value,
                })
              }
            ></textarea>
          {:else}
            <p class="card-text">{post.content}</p>
          {/if}
          <div class="d-flex">
            {#if editInfo.postId === post._id}
              <div class="d-flex gap-3 mt-4">
                <button class="btn btn-success" on:click={handleSaveEdit}>
                  Save
                </button>
                <button
                  class="btn btn-secondary"
                  on:click={() =>
                    (editInfo = {
                      postId: null,
                      editedTitle: "",
                      editedContent: "",
                    })
                  }
                >
                  Cancel
                </button>
              </div>
            {:else}
              <div class="d-flex justify-content-between w-100">
                <button
                  class="btn btn-primary"
                  on:click={() => handleEdit(post._id, post.title, post.content)}
                >
                  Edit
                </button>
                <button class="btn btn-danger" on:click={() => handlePostDelete(post._id)}>
                  Delete
                </button>
              </div>
            {/if}
          </div>
        </div>
      </div>
    {/each}
  </div>
  