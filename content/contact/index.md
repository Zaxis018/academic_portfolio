---
title: "Contact"
date: 2023-10-24
# layout: "contact"
---

<section id="contacts">
    <div class="contact-text">
        <h2>Contact <span>Me</span></h2>
    </div>
    <div class="email">
        <form action="https://api.web3forms.com/submit" method="POST" onsubmit="return validateForm()">
            <input type="hidden" name="access_key" value="bc6751bd-a82b-45e0-90e7-20bdf1e4948d">
            <input type="text" id="name" name="name" placeholder="Your Name" required>
            <input type="text" id="phone" name="number" placeholder="Phone number" required>
            <input type="email" id="emailid" name="email" placeholder="Email id" required>
            <textarea id="message" rows="5" name="message" placeholder="Your message" required></textarea>
            <button type="submit">Send</button>
        </form>
    </div>
</section>

<script>
    function validateForm() {
        const nameInput = document.getElementById('name');
        const phoneInput = document.getElementById('phone');
        const emailInput = document.getElementById('emailid');

        // Validate name (alphabetic characters only)
        const nameRegex = /^[A-Za-z\s]+$/;
        if (!nameRegex.test(nameInput.value)) {
            alert('Please enter a valid name (alphabetic characters only).');
            return false;
        }

        // Validate phone number (numeric, length between 3 and 15)
        const phoneRegex = /^\d+$/;
        if (!phoneRegex.test(phoneInput.value) || phoneInput.value.length < 3 || phoneInput.value.length > 15) {
            alert('Please enter a valid phone number (numeric, 3 to 15 characters).');
            return false;
        }

        // Validate email (using a simple regex for demonstration)
        const emailRegex = /^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,4}$/;
        if (!emailRegex.test(emailInput.value)) {
            alert('Please enter a valid email address.');
            return false;
        }
        // Form is valid
        return true;
    }
</script>
