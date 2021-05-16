<template>
  <v-container>
  <div>
      <h4 class="display-1">Reset password</h4>

	<instructions details="reset password." />

      <p class="body-1">this is reset password page.</p>

            <v-form>
              <v-text-field
                v-model="newPassword.email"
                label="Email"
                name="email"
                prepend-icon="mdi-email"
                type="text"
              />
              <v-text-field
                v-model="newPassword.password"
                id="password"
                label="Password"
                name="password"
                prepend-icon="mdi-lock"
                type="password"
              />
				<v-text-field
				v-model="password.newPassword"
                id="newpassword"
                label="New password"
                name="newpassword"
                prepend-icon="mdi-lock"
                type="password"
				v-bind:rules="rules.password"
				required
              />
            </v-form>
			<v-spacer />
            <v-btn v-on:click="ResetPassword" color="primary">Reset Password</v-btn>
  </div>
  </v-container>
</template>
<script>
import Instructions from "../components/Instructions.vue";

export default {
  name: "ResetPassword",
  components: {
    Instructions, // Use the Instructions component we just imported
  },
  data: function () {
    return {
      valid: false, // Are all the fields in the form valid?

      // Object to collect account data
      newPassword: {
        email: "",
        password: "",
        newpassword: "",
      },
	passwordReset:false,
	dialogHeader: "<no dialogHeader>",
      dialogText: "<no dialogText>",
      dialogVisible: false,

      // Validation rules for the form fields. This functionality is an extension
      // that's part of the Vuetify package. Each rule is a list of functions
      // (note the fat arrows). Vuetify invokes all functions in the list,
      // passing it the content of the associated form field. Functions should
      // return either true (the field passes that validation) or a string
      // containing an error message indicating why the field doesn't pass validation.
      rules: {
        required: [(val) => val.length > 0 || "Required"],
        email: [
          (val) => /\w{3,}@\w{3,}(?:.\w{3,})+$/.test(val) || "Invalid e-mail",
        ],
        password: [
          (val) => /[A-Z]/.test(val) || "Need upper case letter",
          (val) => /[a-z]/.test(val) || "Need lower case letter",
          (val) => /\d/.test(val) || "Need digit",
          (val) => val.length >= 8 || "Minimum 8 characters",
        ],
      },
    };
  },
methods: {
    ResetPassword: function () {
	
	this.passwordReset= false;
	
      this.$axios
        .patch("/reset-password", {
		email:this.newPassword.email,
        password: this.newPassword.password,
		newpassword: this.newPassword.newpassword,
        })
        .then((result) => {
          if (result.data.ok) {
            this.showDialog("Success", result.data.msge);
            this.passwordReset = true;
          } else {
            this.showDialog("Sorry", result.data.msge);
          }
        })
        .catch((err) => this.showDialog("Failed", err));
    },
    showDialog: function (header, text) {
      this.dialogHeader = header;
      this.dialogText = text;
      this.dialogVisible = true;
    },
    hideDialog: function () {
      this.dialogVisible = false;
      if (this.passwordReset) {
        this.$router.push({ name: "home-page" });
      }
    },
  },
};
</script>
