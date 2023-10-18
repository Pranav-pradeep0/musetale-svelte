<script>
    import { onMount } from "svelte";
    import Navbar from "../Home Components/Navbar.svelte";
    import { deleteUser, editUser } from "../../service/allapi";
    import Swal from "sweetalert2";
    import { Link } from "svelte-routing";
    import { BASE_URL } from "../../service/baseurl";
    import UserBlogs from "./UserBlogs.svelte";
  
  
    let profileInfo = JSON.parse(localStorage.getItem("user"));
  
    let editedProfile = {
      name: profileInfo.name,
      username: profileInfo.username,
      email: profileInfo.email,
      password: profileInfo.password,
      profilePic: profileInfo.profilePic,
    };
  
    let isEditing = false;
  
    function handleEdit() {
      isEditing = true;
    }
  
    function handleCancel() {
      editedProfile = {
        name: profileInfo.name,
        username: profileInfo.username,
        email: profileInfo.email,
        password: profileInfo.password,
        profilePic: profileInfo.profilePic,
      };
      isEditing = false;
      setPreview("");
    }
  
    function updatedUserDetails(event) {
      const { value, name } = event.target;
      editedProfile = { ...editedProfile, [name]: value };
    }
  
    let image = "";
    let preview = "";
  
    function setProfile(event) {
      image = event.target.files[0];
      editedProfile = { ...editedProfile, profilePic: event.target.files[0] };
      preview = URL.createObjectURL(event.target.files[0]);
    }
  
    async function handleSave() {
      const headerConfig = {
        "Content-Type": "multipart/form-data",
      };
  
      const data = new FormData();
  
      data.append("name", editedProfile.name);
      data.append("username", editedProfile.username);
      data.append("email", editedProfile.email);
      data.append("password", editedProfile.password);
      if (image) {
        data.append("profilePic", image);
      }
  
      const response = await editUser(profileInfo._id, data, headerConfig);
  
      localStorage.setItem("user", JSON.stringify(response.data));
  
      if (response.status === 200) {
        Swal.fire({
          icon: "success",
          title: "Profile Updated",
          text: "Your profile has been successfully updated!",
        });
      } else {
        Swal.fire({
          icon: "error",
          title: "Error",
          text: "There's an error updating your profile. Please try again.",
        });
      }
  
      isEditing = false;
    }
  
    async function deleteAccount() {
      const result = await Swal.fire({
        title: "Are you sure?",
        text: "You will not be able to recover your account after deletion.",
        icon: "warning",
        showCancelButton: true,
        confirmButtonColor: "#d33",
        cancelButtonColor: "#3085d6",
        confirmButtonText: "Delete",
        cancelButtonText: "Cancel",
      });
  
      if (result.isConfirmed) {
        const response = await deleteUser(profileInfo._id);
        localStorage.clear();
        Swal.fire("Deleted!", "Your account has been deleted.", "success");
        navigate("/home");
        window.location.href = "/home";
      }
    }
  </script>
  
  <div>
    <Navbar />
    <nav aria-label="breadcrumb" class="mx-5 mt-5 pt-4 px-5">
      <ol class="breadcrumb">
        <li class="breadcrumb-item"><Link to="/home">Home</Link></li>
        <li class="breadcrumb-item active" aria-current="page">Profile</li>
      </ol>
    </nav>
    <div class="d-flex profile-conainer p-5 flex-wrap gap-5">
      <div class="me-auto">
        <div class="card bg-dark" style="width: 18rem;">
          {#if preview}
            <img src={preview} class="card-img-top" alt="Profile" />
          {:else if editedProfile.profilePic}
            <img src={`${BASE_URL}/uploads/${profileInfo.profilePic}`} class="card-img-top" alt="" />
          {:else}
            <img src="https://cdn.pixabay.com/photo/2015/10/05/22/37/blank-profile-picture-973460_640.png" class="card-img-top" alt="Profile" />
          {/if}
          {#if isEditing}
            <div class="px-4">
              <input class="form-control mt-4 mb-2" type="file" name="profilePic" on:change={setProfile} />
            </div>
          {:else}
            <div class="mb-4 pb-3"></div>
          {/if}
          <div class="pb-4">
            {#if isEditing}
              <input class="form-control border-0 edit-input" type="text" bind:value={editedProfile.name} name="name" on:input={updatedUserDetails} />
            {:else}
              <h3 class="text-center text-uppercase card-title">{editedProfile.name}</h3>
            {/if}
          </div>
          <ul class="list-group list-group-light list-group-small">
            <li class="list-group-item px-4 text-white">
              Username : {editedProfile.username}
            </li>
            <li class="list-group-item px-4 text-white">
              {#if isEditing}
                <input class="form-control text-white p-0 border-0" type="email" bind:value={editedProfile.email} name="email" on:input={updatedUserDetails} />
              {:else}
                {editedProfile.email}
              {/if}
            </li>
          </ul>
          <div class="card-body d-flex justify-content-evenly mt-3">
            <div>
              {#if isEditing}
                <div class="d-flex justify-content-evenly gap-3 mx-auto">
                  <button class="btn btn-outline-primary" on:click={handleSave}>Save</button>
                  <button class="btn btn-outline-danger text-danger" on:click={handleCancel}>Cancel</button>
                </div>
              {:else}
                <button class="btn btn-outline-primary" on:click={handleEdit}>Edit</button>
              {/if}
            </div>
            <div>
              {#if !isEditing}
                <button class="btn btn-outline-danger text-danger" on:click={deleteAccount}>Delete</button>
              {/if}
            </div>
          </div>
        </div>
      </div>
      <div class="flex-grow mx-auto">
        <UserBlogs />
      </div>
    </div>
  </div>
  