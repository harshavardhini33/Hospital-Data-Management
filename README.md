# EXP 27: HOSPITAL DATA MANAGEMENT

## AIM:
 To create a Hospital patient data management application using React,SpringBoot and SQL.

 ## ALGORITHM:

 1. Create a SpringBoot project with the required specifications.
 
 2. Set up the JDK environment and add the necessary java files.
 
 3. Create a suitable database in PostgresSQL and connect it to your SpringBoot File
 
 4. Create the User Interface using React and define the specific components.
 
 5. Connect the front end to the back end.
 
 6. Run the project and make necessary changes as required.

 ## PROGRAM:

 ### SPRING BOOT:
 ### Hospital.java:
 java
package com.saveetha.employee.emp;
import jakarta.persistence.*;
import jakarta.persistence.criteria.CriteriaBuilder;

import java.time.LocalDate;

@Entity
@Table
public class HospitalController {
    @Id
    @SequenceGenerator(
            name = "hospital_sequence",
            sequenceName = "hospital_sequence",
            allocationSize = 1
    )
    @GeneratedValue(
            strategy = GenerationType.SEQUENCE,
            generator = "hospital_sequence"
    )
    private Long patientId;
    private String patientName;
    private String patientGender;
    private Integer patientAge;
    private LocalDate patientDOA;
    public HospitalController(Long patientId, String patientName, String patientGender, Integer patientAge, LocalDate patientDOA) {
        this.patientId = patientId;
        this.patientName = patientName;
        this.patientGender = patientGender;
        this.patientAge = patientAge;
        this.patientDOA = patientDOA;
    }
    public Long getPatientId() {return patientId;}
    public void setPatientId(Long patientId) {this.patientId = patientId;}
    public String getPatientName() {return patientName;}
    public void setPatientName(String patientName) {this.patientName = patientName;}
    public String getPatientGender() {return patientGender;}
    public void setPatientGender(String patientGender) {this.patientGender = patientGender;}
    public Integer getPatientAge() {return patientAge;}
    public void setPatientAge(Integer patientAge) {this.patientAge = patientAge;}
    public LocalDate getPatientDOA() {return patientDOA;}
    public void setPatientDOA(LocalDate patientDOA) {this.patientDOA = patientDOA;}
    @Override
    public String toString() {
        return "HospitalController{" +
                "patientId=" + patientId +
                ", patientName='" + patientName + '\'' +
                ", patientGender='" + patientGender + '\'' +
                ", patientAge=" + patientAge +
                ", patientDOA=" + patientDOA +
                '}';
    }
}


### HospitalController.java:
java
package com.saveetha.employee.emp;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;
import java.util.List;
@RestController
@RequestMapping(path = "/api/v1/hospital")
public class HospitalController {
    private final HospitalService hospitalService;
    @Autowired
    public HospitalController(HospitalService hospitalService) {
        this.hospitalService = hospitalService;
    }
    @GetMapping("/")
    public List<Hospital> getPatientDetails()
    {
        return hospitalService.displayPatientDetails();
    }
    @PostMapping("/")
    public void postPatient(@RequestBody Hospital hospital){
        employeeService.registerNewPatient(hospital);
    }
    @DeleteMapping(path = {"/{id}"})
    public void deletePatient(@PathVariable("id") Long patientId){
        employeeService.removePatient(patientId);
    }
}


### REACT CODES:

### App.js:
js
import React from 'react';
import './App.css';
import { BrowserRouter as Router, Route, Routes, Link } from "react-router-dom";
import PatientDirectoryComponent from './components/PatientDirectoryComponent/PatientDirectoryComponent';
import PatientRegistrationComponent from './components/PatientRegistrationComponent/PatientRegistrationComponent';
import PatientDeletionComponent from './components/PatientDeletionComponent/PatientDeletionComponent';
import HeaderComponent from './components/HeaderComponent/HeaderComponent';

function App() {
  return (
    <Router>
      <div className='container'>
        <center>
        <HeaderComponent/>
        </center>
      
      <nav className='nav-menu'>
        <Link to='/'>Patient List</Link>
        <Link to='/admin/add'>Admit Patients</Link>
        <Link to='/admin/delete'>Discharge Patients</Link>
      </nav>
      <Routes>
        <Route exact path = '/' element={<PatientDirectoryComponent/>}></Route>
        <Route path = '/admin/add' element={<PatientRegistrationComponent/>}></Route>
        <Route path = '/admin/delete' element={<PatientDeletionComponent/>}></Route>
      </Routes>
      </div>
    </Router>
  );
}
export default App;


### PatientDirectoryComponent.js:
js
import React, { useEffect, useState } from 'react';
import './PatientDirectoryComponent.css'; 

function PatientDirectoryComponent() {
  const [patient, setPatient] = useState([]);

  useEffect(() => {
    fetchPatient();
  }, []);

  const fetchPatient = async () => {
    try {
      const response = await fetch('http://localhost:8080/api/v1/hospital/');
      const data = await response.json();
      setPatient(data);
    } catch (error) {
      console.error('Error:', error);
    }
  };

  return (
    <div className="patient-list">
      {patient.map((patient) => (
        <div className="patient-card" key={patient.patientId}>
          <p>Patient ID: {patient.patientId}</p>
          <p>Patient Name: {patient.patientName}</p>
          <p>Patient Gender: {patient.patientGender}</p>
          <p>Patient Age: {patient.patientAge}</p>
          <p>Patient Date of Admission: {patient.patientDOA}</p>
        </div>
      ))}
    </div>
  );
};

export default PatientDirectoryComponent;


### PatientRegistrationComponent.js:
js
import React, { useState } from "react";
import './PatientRegistrationComponent.css'

function PatientRegistrationComponent() {
    const [patient, setPatient] = useState({
        patientName: '',
        patientQuantity: '',
        patientPrice: ''
    });

    const handleChange = (event) => {
        const {name, value} = event.target;
        setPatient({...patient, [name]:value});
    };

    const handleFormSubmit =async (event) => {
        event.preventDefault();
        await fetch('http://localhost:8080/api/v1/hospital/', {
            method: 'POST',
            headers: {
                'Content-type': 'application/json'
            },
            body: JSON.stringify(product)
        })

        .then((response) => {
            if (response.status == 500)
            {
                alert(`Error!`)
            }
            else{
                alert(`Data of ${patient.patientName} is added successfully`)
                window.location.href = '/'
            }
        })
  };
            
    return(
        <form onSubmit={handleFormSubmit}>
            <label>
                Patient Name: 
                <input type="text" name="patientName" value={patient.patientName} onChange={handleChange} required/>
            </label> 
            <label>
                Patient Gender: 
                <select name="gender" id="gend">
                    <option value="F">FEMALE</option>
                    <option value="M">MALE</option>
                    <option value="0">OTHERS</option>
                </select>
            </label> 
            <label>
                Patient Age: 
                <input type="text" name="patientAge" value={patient.patientAge} onChange={handleChange} required/>
            </label>
            <label>
                Patient Date of Admission: 
                <input type="date" name="patientDOA" value={patient.patientDOA} onChange={handleChange} required/>
            </label>

            <button type="submit">SUBMIT</button> 
        </form>
    )
}

export default PatientRegistrationComponent;


### PatientDeletionComponent.js:
js
import React, { useState } from 'react';
import './PatientDeletionComponent.css'
function PatientDeletionComponent() {
  const [PatientID, setPatientID] = useState('');

  const handleChange = (event) => {
    setPatientID(event.target.value);
  };
  const handleSubmit = async(event) => {
    event.preventDefault();
    await fetch(`http://localhost:8080/api/v1/hospital/${PatientID}`,{
      method: 'DELETE'
    })
    .then((response) => {
            if (response.status == 500)
            {
                alert(`Error!`)
            }
            else{
                alert(`Data deleted successfully`)
                window.location.href = '/'
            }
        })
  };
  return (
    <form onSubmit={handleSubmit}>
      <label>
        Patient ID:
        <input
          type="number"
          name="patientID"
          value={patientID}
          onChange={handleChange}
          required
        />
      </label>
      <button type="submit">Submit</button>
    </form>
  );
};

export default PatientDeletionComponent;



## OUTPUT:
![image](https://github.com/Aashima02/Hospital-Data-Management/assets/93427086/5bf0fb32-cca5-4257-9162-8513d13a8b2a)

![image](https://github.com/Aashima02/Hospital-Data-Management/assets/93427086/f81ac817-4f09-4733-8c2c-ff0519d76313)



## RESULT:

 Thus, a Hospital patient data management application is created using React, springboot and SQL.
