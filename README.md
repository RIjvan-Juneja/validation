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

///  post apis

// Import necessary modules
const express = require('express');
const mysql = require('mysql2');
const bodyParser = require('body-parser');

// Create Express app
const app = express();
const port = 3000;

// Configure MySQL connection
const connection = mysql.createConnection({
  host: 'localhost',
  user: 'your_mysql_user',
  password: 'your_mysql_password',
  database: 'employee_management'
});

// Use body-parser middleware
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));

// API endpoint for inserting data
app.post('/insert', (req, res) => {
  // Extract data from the request body
  const {
    first_name,
    last_name,
    email,
    degree,
    university,
    graduation_year,
    technologies,
    technology_proficiency,
    reference_name,
    reference_contact
  } = req.body;

  // Insert into basic_details table
  connection.query(
    'INSERT INTO basic_details (first_name, last_name, email) VALUES (?, ?, ?)',
    [first_name, last_name, email],
    (error, results) => {
      if (error) {
        console.error(error);
        res.status(500).json({ message: 'Internal Server Error' });
        return;
      }

      const employee_id = results.insertId;

      // Insert into education_details table
      connection.query(
        'INSERT INTO education_details (employee_id, degree, university, graduation_year) VALUES (?, ?, ?, ?)',
        [employee_id, degree, university, graduation_year],
        (error) => {
          if (error) {
            console.error(error);
            res.status(500).json({ message: 'Internal Server Error' });
            return;
          }

          // Insert into technology_details table
          technologies.forEach((tech, index) => {
            const proficiency = technology_proficiency[index];
            connection.query(
              'INSERT INTO technology_details (employee_id, technology_name, proficiency) VALUES (?, ?, ?)',
              [employee_id, tech, proficiency],
              (error) => {
                if (error) {
                  console.error(error);
                  res.status(500).json({ message: 'Internal Server Error' });
                  return;
                }
              }
            );
          });

          // Insert into reference_details table
          reference_name.forEach((name, index) => {
            const contact = reference_contact[index];
            connection.query(
              'INSERT INTO reference_details (employee_id, reference_name, reference_contact) VALUES (?, ?, ?)',
              [employee_id, name, contact],
              (error) => {
                if (error) {
                  console.error(error);
                  res.status(500).json({ message: 'Internal Server Error' });
                  return;
                }
              }
            );
          });

          res.status(200).json({ message: 'Data inserted successfully' });
        }
      );
    }
  );
});

// Start the server
app.listen(port, () => {
  console.log(`Server is running on http://localhost:${port}`);
});
