<template>

    <div class="container w-75 border rounded">
        <div class="row">
            <div class="col-12">
                <h1 class="text-center py-3">Login</h1>
            </div>

            <div class="col-12">
                <form>
                    <div class="form-group my-3">
                        <input type="email" class="form-control" id="username" placeholder="Email">
                    </div>

                    <div class="form-group my-3">
                        <input type="password" class="form-control" id="password" placeholder="Password">
                    </div>

                    <div class="d-flex button justify-content-center mt-3">
                        <button type="submit" class="btn btn-primary">Login</button>
                    </div>

                    <div class="d-flex justify-content-center mb-2">
                        <span>Don't have an account? <router-link to="/Register">Signup</router-link></span>
                    </div>
                </form>
            </div>
        </div>
    </div>

</template>

<script>
export default {
    name: 'Login',
}
</script>

<style lang="scss" scoped>
    div.container{
        box-shadow: rgba(17, 12, 46, 0.15) 0px 48px 100px 0px;

        a{
            text-decoration: none;
        }
    }

</style>