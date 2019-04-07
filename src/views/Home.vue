<template>
  <div class="home">
    <h1>{{ opener }}</h1>
    <hr>
    <button v-on:click="toggleCreate()">Add Contact</button>
    <div v-if="createInput === 'on'">
      <p>First Name: <input type="text" v-model="newContactFirstName"></p>
      <p>Last Name: <input type="text" v-model="newContactLastName"></p>
      <p>Bio: <input type="text" v-model="newContactBio"></p>
      <p>Email: <input type="text" v-model="newContactEmail"></p>
      <p>Phone #: <input type="text" v-model="newContactPhoneNumber">
      <p><button v-on:click="addNewContact()">Create</button></p>
      </p>
    </div>
    <hr>
    <div v-for="contact in contacts">
      <p>{{ contact.first_name }}, {{ contact.last_name }}</p>
      <button v-on:click="toggleInfo(contact)">Info</button>
        <div v-if="contact === currentContact">
          <p>{{ contact.bio }}</p>
          <p>{{ contact.email }}</p>
          <p>{{ contact.phone_number }}</p> 
          <p><button v-on:click="toggleUpdate(contact)">Update Info</button>
          <button v-on:click="deleteContact(contact)">Delete Contact</button></p>
          <div v-if="contact === updateContact">
            <p>First Name: <input type="text" v-model="contact.first_name"></p>
            <p>Last Name: <input type="text" v-model="contact.last_name"></p>
            <p>Bio: <input type="text" v-model="contact.bio"></p>
            <p>Email: <input type="text" v-model="contact.email"></p>
            <p>Phone #: <input type="text" v-model="contact.phone_number"></p>
            <button v-on:click="contactUpdate(contact)">Update Contact</button>

          </div>
        </div>
      <hr>
    </div>

  </div>
</template>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      opener: "Contacts",
      contacts: [],
      currentContact: [],
      updateContact: [],
      createInput: "off",
      newContactFirstName: "",
      newContactLastName: "",
      newContactBio: "",
      newContactEmail: "",
      newContactPhoneNumber: ""
    };
  },
  created: function() {
    axios.get("/api/contacts").then(response => {
      this.contacts = response.data;
    });
  },
  methods: {
    toggleInfo: function(aContact) {
      if (this.currentContact === aContact) {
        this.currentContact = {};
      } else {
        this.currentContact = aContact;
      }  
    },
    toggleUpdate: function(aContact) {
      if (this.updateContact === aContact) {
        this.updateContact = {};
      } else {
        this.updateContact = aContact;
      }  
    },
    contactUpdate: function(theContact) {
      var params = {
        first_name: theContact.first_name,
        last_name: theContact.last_name,
        bio: theContact.bio,
        email: theContact.email,
        phone_number: theContact.phone_number,

      };
      axios.patch("/api/contacts/" + theContact.id, params).then(response => {
        console.log(response);
        theContact = response.data;
      });
    },
    deleteContact: function(theContact) {
      var params = {
        id: theContact.id
      };
      var index = this.contacts.indexOf(theContact);
      this.contacts.splice(index, 1);

      axios.delete("/api/contacts/" + theContact.id, params).then(response => {
        console.log(response);
        theContact = response.data;
      });
    },
    toggleCreate: function() {
      if (this.createInput === "on") {
        this.createInput = "off";
      } else {
        this.createInput = "on";
      }
    },
    addNewContact: function() {
      var params = {
        first_name: this.newContactFirstName,
        last_name: this.newContactLastName,
        bio: this.newContactBio,
        email: this.newContactEmail,
        phone_number: this.newContactPhoneNumber
      };
      this.createInput = "off";
      console.log(params);
      axios.post("/api/contacts/", params).then(response => {
        console.log(response.data);
        this.contacts.push(response.data);
      });
    }
  }
};
</script>
