<script>
    import { registerLogic, signinLogic } from '../../service/allapi';
    import { Link } from 'svelte-routing';
    import Swal from 'sweetalert2';
    import { BASE_URL } from '../../service/baseurl';
  
    let registerData = {
      username: '',
      email: '',
      name: '',
      password: ''
    };
  
    let signinData = {
      username: '',
      password: ''
    };
  
    let image;
  
    const setProfile = (e) => {
      image = e.target.files[0];
    };
  
    const register = async () => {
      const { username, email, name, password } = registerData;
  
      const headerConfig = {
        "Content-Type": "multipart/form-data"
      };
  
      const data = new FormData();
  
      data.append('profilePic', image);
      data.append('username', username);
      data.append('email', email);
      data.append('name', name);
      data.append('password', password);
  
      const response = await registerLogic(data, headerConfig);
  
      if (response.status == 201) {
        window.location.reload();
      } else {
        alert('Invalid Details');
      }
    };
  
    const signin = async () => {
      const response = await signinLogic(signinData);
  
      if (response.status < 300) {
        const Toast = Swal.mixin({
          toast: true,
          position: 'top-end',
          showConfirmButton: false,
          timer: 3000,
          timerProgressBar: true,
          didOpen: (toast) => {
            toast.addEventListener('mouseenter', Swal.stopTimer);
            toast.addEventListener('mouseleave', Swal.resumeTimer);
          }
        });
  
        Toast.fire({
          icon: 'success',
          title: 'Signed in successfully'
        });
      }
  
      if (response.status === 200) {
        const responseUserObject = JSON.stringify(response.data.user);
        localStorage.setItem('user', responseUserObject);
  
        setTimeout(() => {
          window.location.reload();
        }, 1500);
      } else {
        alert('User not found');
      }
    };
  
    const responseUserObject = localStorage.getItem('user');
    let responseUserObjectparsed;
  
    if (responseUserObject) {
      responseUserObjectparsed = JSON.parse(responseUserObject);
    }
  
    const signupinput = (e) => {
      registerData = { ...registerData, [e.target.name]: e.target.value };
    };
  
    const signininput = (e) => {
      signinData = { ...signinData, [e.target.name]: e.target.value };
    };
  
    const signinUsernameinput = (e) => {
      const lowercaseValue = e.target.value.toLowerCase();
      signinData = { ...signinData, [e.target.name]: lowercaseValue };
    };
  
    const handleusernameinput = (e) => {
      const lowercaseValue = e.target.value.toLowerCase();
      registerData = { ...registerData, [e.target.name]: lowercaseValue };
    };
  
    const logoutFunct = () => {
      localStorage.clear();
      window.location.href = '/home';
    };
  </script>
  
  {#if responseUserObject}
    <!-- Navbar when user is logged in -->
    <nav class="navbar navbar-expand-lg navbar-dark px-5 bg-dark">
      <!-- Left elements -->
      <div class="container-fluid justify-content-between">
        <!-- Brand -->
        <div class="d-flex">
          <Link to={'/'} class="navbar-brand me-2 mb-1 d-flex align-items-center">
            <i class="fa-solid fa-book-open text-danger"></i>
          </Link>
        </div>
        <!-- Center elements -->
        <ul class="navbar-nav flex-row d-none d-md-flex">
          <li class="nav-item">
            <h5 class="my-auto">Hello {responseUserObjectparsed.name}</h5>
          </li>
        </ul>
        <!-- Right elements -->
        <ul class="navbar-nav flex-row">
          <li class="nav-item dropdown">
            <a class="nav-link d-flex align-items-center" id="navbarDropdownMenuLink" role="button" data-mdb-toggle="dropdown" aria-expanded="false">
              <img src={`${BASE_URL}/uploads/${responseUserObjectparsed.profilePic}`} class="rounded-circle" height="30" loading="lazy"  alt=""/>
              <i class="fa-solid fa-caret-down ms-2"></i>
            </a>
            <ul class="dropdown-menu nav-drop-menu" aria-labelledby="navbarDropdownMenuLink">
              <li>
                <Link to={'/profile'} class="dropdown-item">My profile</Link>
              </li>
              <li>
                <button class="dropdown-item text-primary" on:click={logoutFunct}>Logout</button>
              </li>
            </ul>
          </li>
        </ul>
      </div>
    </nav>
  {:else}
    <!-- Navbar when user is not logged in -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
      <div class="container">
        <Link to={'/'} class="navbar-brand me-2">
          <i class="fa-solid fa-book-open text-danger"></i>
        </Link>
        <button class="navbar-toggler" type="button" data-mdb-toggle="collapse" data-mdb-target="#navbarButtonsExample" aria-controls="navbarButtonsExample" aria-expanded="false" aria-label="Toggle navigation">
          <i class="fas fa-bars"></i>
        </button>
        <div class="collapse navbar-collapse" id="navbarButtonsExample">
          <ul class="navbar-nav me-auto mb-2 mb-lg-0"></ul>
          <div class="d-flex align-items-center">
            <button type="button" class="btn btn-link px-3 me-2" data-mdb-toggle="modal" data-mdb-target="#loginModal">Login</button>
            <button type="button" class="btn btn-primary me-3" data-mdb-toggle="modal" data-mdb-target="#signupModal">Sign up for free</button>
          </div>
        </div>
      </div>
    </nav>
  
    <!-- Login Modal -->
    <div class="modal fade" id="loginModal" tabindex="-1" aria-labelledby="loginModalLabel" aria-hidden="true">
      <div class="modal-dialog modal-dialog-centered">
        <div class="modal-content bg-dark p-4">
          <div class="modal-header border-0">
            <h5 class="modal-title" id="loginModalLabel"> </h5>
            <button type="button" class="btn-close" data-mdb-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            <!-- ... (Login modal content from original code) -->
          </div>
        </div>
      </div>
    </div>
  
    <!-- Signup Modal -->
    <div class="modal fade" id="signupModal" tabindex="-1" aria-labelledby="signupModalLabel" aria-hidden="true">
      <div class="modal-dialog modal-dialog-centered">
        <div class="modal-content bg-dark p-4">
          <div class="modal-header border-0">
            <h5 class="modal-title" id="signupModalLabel"> </h5>
            <button type="button" class="btn-close" data-mdb-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            <!-- ... (Signup modal content from original code) -->
          </div>
        </div>
      </div>
    </div>
  {/if}
  