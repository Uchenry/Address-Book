# Address-Book
This application helps to save and  keep track of various contacts, their numbers, and emails.
<!DOCTYPE html>
<html>
<head>
  <title></title>
  <script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
</head>
  <style>
       /*CSS reset with universal selector*/

* {
  margin: 0;
  padding: 0;
  border: 0;
  outline: 0;
  font-size: 100%;
  vertical-align: baseline;
  background: transparent;
}

body {
  background-color: #eee;
  font-family: Helvetica;
}

a {
  color: #333;
  text-decoration: none;
}

a:hover,
a:focus,
a:active,
a.active {
  color: #222;
  text-decoration: none;
}

.bg-green {
  --rgb: 0, 100, 0;
}

.bg-red {
  --rgb: 220, 53, 69;
}

.bg-green,
.bg-red {
  --alpha: 0.9;
  background-color: rgba(var(--rgb), var(--alpha));
}

button {
  --alpha: 0.8;
  color: white;
  cursor: pointer;
  padding: 10px 20px;
  border-radius: 3px;
}

button:hover,
button:focus,
button:active,
button.active {
  --alpha: 1;
  color: white;
}

.hide {
  display: none;
}

.d-inline {
  display: inline;
  vertical-align: middle;
}

.background-red {
  background-color: #dc3545;
}

.navbar {
  color: white;
  padding: 20px;
  background-color: green;
  box-shadow: 0 0 10px #9e9e9e;
}

.wrapper {
  padding: 50px;
  display: grid;
  grid-gap: 1em;
  grid-template-columns: 1fr 1fr 1fr;
  grid-auto-rows: minmax(100px, auto);
}

/* Form Elements*/

.field {
  margin: 20px 0;
  display: flex;
  flex-direction: row;
}

.field input:not([type="file"]) {
  margin-left: 10px;
  border-bottom: 1px solid #333;
  flex: 1;
}

.field .req {
  color: red;
}

/* Registration Form */

form.registration button[type="submit"] {
  width: 100%;
}

/* Contact List */
.contact-list {
  padding: 0 30px;
}

.contact-list li {
  cursor: pointer;
  list-style-type: none;
}

.contact-list-item {
  margin-bottom: 30px;
}

/* Contact Item */
.contact-item {
  text-align: center;
}

.contact-item p {
  margin-bottom: 20px;
}

  </style>
<body>
  <div class="navbar">
  <h3>My Address Book</h3>
</div>
<div class="wrapper">
  <form class="registration">
    <h2>Enter Your Details</h2>
    <input name="id" type="hidden" />
    <div class="field">
      <label for="firstname" class="label">First Name</label>
      <span class="req">*</span>
      <input type="text" id="firstname" name="firstname" required>
    </div>

    <div class="field">
      <label for="firstname" class="label">Last Name</label>
      <span class="req">*</span>
      <input type="text" id="lastname" name="lastname" required>
    </div>

    <div class="field">
      <label for="email" class="label">Email Address</label>
      <span class="req">*</span>
      <input type="email" id="email" name="email">
    </div>

    <button type="submit" class="bg-green" id="form-submit-btn">Add Contact</button>
  </form>
  <div class="display contact-list">
    <ul class="contact-list-ul">
    </ul>
  </div>
  <div class="display contact-item">
  </div>
</div>

<script>
       $(document).ready(function () {
  const contactListArray = [
    {
      firstname: 'Iveren',
      lastname: 'Shaguy',
      email: 'iveren@shaguy.com'
    },
    {
      firstname: 'Olisa',
      lastname: 'Emodi',
      email: 'olisa@emodi.com'
    }
  ];
  const firstname = $('input[name="firstname"]');
  const lastname = $('input[name="lastname"]');
  const email = $('input[name="email"]');
  const form = $('form');
  const contactListItem = (contact, index) => (
    `<li class="contact-list-item" id=${index}>
      <a>
        <h3 class="d-inline">${contact.firstname} ${contact.lastname}</h3>
      </a>
    </li>`
  );
  const contactItem = (contact, index) => (
    `<div class="contact" id=${index}>
    <p>${contact.firstname} ${contact.lastname}</p>
    <p class=${index}>${contact.email}</p>
    <div class="contact-item-btns">
      <button class="bg-green" id="edit-btn">Edit</button>
      <button class="bg-red" id="delete-btn">Delete</button>
    </div>
   </div>`
  );

  // on page laod, render list and contact-item for first contact
  renderList();
  renderContactItem(contactListArray[0], 0);

  function renderList() {
    // sort contact list by firstname using localeCompare which works irrespective of case and symbol
    const sortFunc = (a, b) => a.firstname.localeCompare(b.firstname);
    const sortedContactList = contactListArray.sort(sortFunc);
    const list = sortedContactList.map((item, index) => contactListItem(item, index));
    // add mapped list to contact-list-ul
    $('.contact-list-ul').html(list);
  }


  function renderContactItem(contact, index) {
    const item = contactItem(contact, index);
    // add item to contact-item
    $('.contact-item').html(item);
  }

  function addContact(newContact) {
    // add to contact list
    contactListArray.push(newContact);
    renderList();
    renderContactItem(newContact, contactListArray.length - 1);
  }
    
  function updateContact(contact, id) {
    contactListArray[id] = contact;
    renderList();
    renderContactItem(contact, id);
  }

  form.submit(function (event) {
    event.preventDefault();

    const contact = {
      firstname: firstname.val(),
      lastname: lastname.val(),
      email: email.val(),
    };
    
    const id = $('input[name="id"]').val().trim();

    if(id) {
      // update contact if Id field is filled
      updateContact(contact, id);
      // clear hidden input field
      $('input[name="id"]').val('');
    } else {
      // add new contact
      addContact(contact);
    } 

    this.reset();
    // change button value back to add contact
    $('#form-submit-btn').html('Add Contact');
  });

  // we use .on because we are generating dynamic content
  // we bind .on to parent elem that is a static elem already available in dom
  // .contact-list-item is the particular item we are clicking on
  $('.contact-list').on('click', '.contact-list-item', function () {
    // this is contact list item that we clicked on;
    const listItem = $(this);
    const index = listItem.attr('id');

    const contact = contactListArray[index];
    renderContactItem(contact, index);
    // $('#firstname').focus();
  });

  // $('.contact-item').on('click', '.contact-item-btns', function(){

  //     //const editInput = $(this);

  //     $('#firstname').focus();

      

  //});

      $('.contact-item').on('click', '#edit-btn', function() {
        // change button text to Save
        $('#form-submit-btn').html('Save');
 
        const index = $('.contact').attr('id');
        const contact = contactListArray[index];
        
        // fill in form values
        $('#firstname').val(contact.firstname);
        $('#lastname').val(contact.lastname);
        $('#email').val(contact.email);
        $('input[name="id"]').val(index);
     });
    
    $('.contact-item').on('click', '#delete-btn', function(){
           const index = $('.contact').attr('id');
           contactListArray.splice(index, 1);
           renderList();
           renderContactItem(contactListArray[0], 0);
       
     });
   });
   
</script>
</body>
</html>
