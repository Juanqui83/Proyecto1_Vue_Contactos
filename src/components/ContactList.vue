<template>
    <div>
      <h2>Contact List</h2>
      <input v-model="filter" placeholder="Search by name or email" />
      <ul>
        <li v-for="contact in filteredContacts" :key="contact.id">
          {{ contact.name }} - {{ contact.email }}
          <button @click="editContact(contact)">Edit</button>
          <button @click="deleteContact(contact.id)">Delete</button>
        </li>
      </ul>
      <button @click="showAddForm = true">Add Contact</button>
      <div v-if="showAddForm">
        <h3>Add New Contact</h3>
        <form @submit.prevent="addContact">
          <input v-model="newContact.name" placeholder="Name" />
          <input v-model="newContact.email" placeholder="Email" />
          <input v-model="newContact.address" placeholder="Address" />
          <input v-model="newContact.phone" placeholder="Phone" />
          <input v-model="newContact.country" placeholder="Country" />
          <input v-model="newContact.city" placeholder="City" />
          <button type="submit">Add</button>
          <button @click="showAddForm = false">Cancel</button>
        </form>
      </div>
      <div v-if="editFormVisible">
        <h3>Edit Contact</h3>
        <form @submit.prevent="updateContact">
          <input v-model="currentContact.name" placeholder="Name" />
          <input v-model="currentContact.email" placeholder="Email" />
          <input v-model="currentContact.address" placeholder="Address" />
          <input v-model="currentContact.phone" placeholder="Phone" />
          <input v-model="currentContact.country" placeholder="Country" />
          <input v-model="currentContact.city" placeholder="City" />
          <button type="submit">Update</button>
          <button @click="editFormVisible = false">Cancel</button>
        </form>
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        contacts: [],
        filter: '',
        newContact: {
          name: '',
          email: '',
          address: '',
          phone: '',
          country: '',
          city: ''
        },
        currentContact: null,
        showAddForm: false,
        editFormVisible: false
      };
    },
    computed: {
      filteredContacts() {
        return this.contacts.filter(contact => 
          contact.name.toLowerCase().includes(this.filter.toLowerCase()) ||
          contact.email.toLowerCase().includes(this.filter.toLowerCase())
        );
      }
    },
    methods: {
      fetchContacts() {
        axios.get('/contacts.json')
          .then(response => {
            this.contacts = response.data;
          });
      },
      addContact() {
        this.newContact.id = this.contacts.length + 1;
        this.contacts.push({...this.newContact});
        this.newContact = {
          name: '',
          email: '',
          address: '',
          phone: '',
          country: '',
          city: ''
        };
        this.showAddForm = false;
      },
      deleteContact(id) {
        this.contacts = this.contacts.filter(contact => contact.id !== id);
      },
      editContact(contact) {
        this.currentContact = {...contact};
        this.editFormVisible = true;
      },
      updateContact() {
        const index = this.contacts.findIndex(contact => contact.id === this.currentContact.id);
        if (index !== -1) {
          this.contacts.splice(index, 1, this.currentContact);
        }
        this.currentContact = null;
        this.editFormVisible = false;
      }
    },
    mounted() {
      this.fetchContacts();
    }
  };
  </script>

