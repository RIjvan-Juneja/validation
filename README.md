# validation

function validateForm() {
            const requiredFields = ['first_name', 'last_name', 'email', 'phone_number', 'relationship_status', 'dob', 'address', 'state', 'city'];

            for (const field of requiredFields) {
                const value = document.getElementById(field).value.trim();
                const errorSpan = document.getElementById(`error_${field}`);

                if (value === '') {
                    errorSpan.textContent = `Please fill in the ${field.replace('_', ' ')}`;
                    return false;
                } else {
                    errorSpan.textContent = ''; // Clear error message if field is filled
                }
            }

            // Additional validation logic can be added as needed

            return true;
        }
