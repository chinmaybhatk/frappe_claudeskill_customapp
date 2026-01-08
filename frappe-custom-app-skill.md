# Frappe Custom App Development Skill

> **A comprehensive skill for building production-ready custom applications in the Frappe Framework ecosystem**
> 
> This skill provides complete guidance for developing custom Frappe apps, from local development to production deployment on any platform (Frappe Cloud, AWS, local servers, or other cloud providers).

---

## Skill Metadata

**Skill Type:** Technical Development Guide  
**Domain:** Full-Stack Web Application Development  
**Framework:** Frappe Framework (Python/JavaScript)  
**Complexity:** Intermediate to Advanced  
**Scope:** Complete lifecycle from setup to production deployment

**Compatible With:**
- ✅ Claude.ai Projects (add as project knowledge/skill)
- ✅ Claude Code (add to workspace as reference)
- ✅ Manual reference for developers

**Usage Context:**
Use this skill when:
- Building new Frappe applications
- Extending existing Frappe apps (ERPNext, FCRM, etc.)
- Troubleshooting Frappe development issues
- Planning Frappe app architecture
- Deploying Frappe apps to production
- Learning Frappe framework patterns

---

## How to Use This Skill

### For Claude.ai Users

**Adding to Your Project:**
1. Open your Claude Project
2. Go to Project Knowledge
3. Upload this file or copy contents
4. Reference it in conversations: "Using the Frappe development skill, help me create a custom DocType..."

**Example Prompts:**
- "Using the Frappe skill, help me set up a new app for inventory management"
- "Based on the Frappe development guide, review this controller code for best practices"
- "Reference the deployment section to help me deploy on AWS"

### For Claude Code Users

**Adding to Workspace:**
1. Place this file in your project directory (e.g., `/docs/frappe-skill.md`)
2. Claude Code will automatically reference it when working on Frappe projects
3. Use in context: "Following the skill guide, create a DocType for..."

**Example Usage:**
```bash
# Claude Code will reference the skill automatically when:
# - Creating new Frappe apps
# - Editing DocType controllers
# - Writing client scripts
# - Setting up deployment configs
```

### For Developers (Manual Reference)

- Bookmark this document
- Use as a quick reference during development
- Follow step-by-step for new projects
- Jump to specific sections as needed

---

## Working with Business Requirements Documents

### Can This Skill Complete Development from a BRD?

**Yes, this skill enables AI-assisted development from Business Requirements Documents (BRD) to production deployment.**

When combined with Claude.ai or Claude Code, this skill provides:
- ✅ **BRD Analysis** - Understanding and breaking down requirements
- ✅ **Architecture Planning** - Translating requirements to Frappe architecture
- ✅ **DocType Design** - Creating appropriate data models
- ✅ **Implementation Guidance** - Step-by-step development
- ✅ **Code Generation** - Controllers, APIs, client scripts
- ✅ **Testing Strategy** - Test cases from requirements
- ✅ **Deployment Planning** - Platform selection and deployment

### End-to-End Development Workflow

#### Phase 1: Requirements Analysis (with Claude)

**Input:** Business Requirements Document

**Process:**
```
You: "I have a BRD for a hospital management system. Help me analyze 
     the requirements and plan the Frappe app architecture.
     
     [Paste/Upload BRD]"

Claude: [Analyzes BRD using skill knowledge]
        
        Analysis Complete. I've identified:
        
        1. CORE MODULES:
           - Patient Management
           - Appointment Scheduling
           - Doctor Management
           - Billing & Payments
           - Inventory Management
           
        2. SUGGESTED DOCTYPES (15 total):
           
           Master DocTypes:
           - Patient (parent)
           - Doctor (parent)
           - Department (parent)
           - Medicine (parent)
           
           Transaction DocTypes:
           - Appointment (submittable)
           - Consultation (submittable)
           - Invoice (submittable)
           - Prescription (child table)
           
        3. WORKFLOWS NEEDED:
           - Appointment: Draft → Scheduled → Completed → Cancelled
           - Invoice: Draft → Submitted → Paid
           
        4. REPORTS REQUIRED:
           - Daily Appointments (Query Report)
           - Revenue by Department (Script Report)
           - Patient History (Script Report)
           
        5. INTEGRATIONS:
           - SMS gateway for reminders
           - Payment gateway for online payments
           - Lab equipment APIs
           
        Would you like me to start with detailed DocType specifications?
```

**Output:**
- Complete architecture plan
- DocType list with relationships
- Workflow definitions
- Integration points identified

#### Phase 2: DocType Design & Creation

**Process:**
```
You: "Yes, start with the Patient DocType. What fields should it have?"

Claude: [References skill's DocType Development section]
        
        PATIENT DOCTYPE SPECIFICATION:
        
        Module: Hospital Management
        Type: Parent DocType
        Is Submittable: No
        Track Changes: Yes
        
        FIELDS:
        
        Section: Basic Information
        - patient_id (Data, Read Only, Auto-generated)
        - first_name (Data, Required)
        - last_name (Data, Required)
        - full_name (Data, Read Only) [Computed]
        - date_of_birth (Date, Required)
        - age (Int, Read Only) [Computed]
        - gender (Select: Male, Female, Other, Required)
        - blood_group (Select: A+, A-, B+, B-, AB+, AB-, O+, O-)
        
        Section: Contact Information
        - mobile (Data, Required, Unique)
        - email (Data)
        - address (Text)
        - city (Data)
        - state (Link to State)
        - pincode (Data)
        
        Section: Emergency Contact
        - emergency_contact_name (Data)
        - emergency_contact_phone (Data)
        - emergency_contact_relation (Data)
        
        Section: Medical Information
        - allergies (Table: Patient Allergy)
          - allergy_type (Data)
          - severity (Select: Mild, Moderate, Severe)
          - notes (Text)
        - chronic_conditions (Table: Chronic Condition)
          - condition (Data)
          - diagnosed_date (Date)
        
        Section: Insurance
        - has_insurance (Check)
        - insurance_provider (Data)
        - insurance_number (Data)
        - insurance_validity (Date)
        
        NAMING: PAT-.YYYY.-.#####
        
        Now I'll help you create this in Frappe...
```

**Output:**
- Complete field specifications
- Validation rules
- Computed fields logic
- Child table structures

#### Phase 3: Controller Logic Implementation

**Process:**
```
You: "Generate the controller code for Patient with validation logic"

Claude: [Generates code following skill patterns]
```

**Generated Code:**
```python
# hospital_management/doctype/patient/patient.py

import frappe
from frappe.model.document import Document
from frappe.utils import getdate, date_diff, today
from frappe import _

class Patient(Document):
    def autoname(self):
        """Auto-generate patient ID"""
        from frappe.model.naming import make_autoname
        self.name = make_autoname("PAT-.YYYY.-.#####")
    
    def before_save(self):
        """Set computed fields before saving"""
        self.set_full_name()
        self.calculate_age()
    
    def validate(self):
        """Validation logic"""
        self.validate_date_of_birth()
        self.validate_mobile_number()
        self.validate_insurance()
    
    def set_full_name(self):
        """Compute full name from first and last name"""
        self.full_name = f"{self.first_name} {self.last_name or ''}".strip()
    
    def calculate_age(self):
        """Calculate age from date of birth"""
        if self.date_of_birth:
            self.age = date_diff(today(), self.date_of_birth) // 365
    
    def validate_date_of_birth(self):
        """Ensure date of birth is not in future"""
        if self.date_of_birth and getdate(self.date_of_birth) > getdate(today()):
            frappe.throw(_("Date of Birth cannot be in the future"))
    
    def validate_mobile_number(self):
        """Validate mobile number format"""
        if self.mobile:
            # Remove spaces and hyphens
            mobile = self.mobile.replace(" ", "").replace("-", "")
            if not mobile.isdigit() or len(mobile) != 10:
                frappe.throw(_("Mobile number must be 10 digits"))
            
            # Check for duplicates
            existing = frappe.db.exists("Patient", {
                "mobile": self.mobile,
                "name": ["!=", self.name]
            })
            if existing:
                frappe.throw(_("Patient with this mobile number already exists"))
    
    def validate_insurance(self):
        """Validate insurance details"""
        if self.has_insurance:
            required_fields = ["insurance_provider", "insurance_number"]
            for field in required_fields:
                if not self.get(field):
                    frappe.throw(
                        _("{0} is required when insurance is enabled").format(
                            frappe.get_meta("Patient").get_label(field)
                        )
                    )
            
            # Check insurance validity
            if self.insurance_validity and getdate(self.insurance_validity) < getdate(today()):
                frappe.msgprint(_("Insurance has expired"), indicator="orange", alert=True)
    
    def after_insert(self):
        """Actions after patient creation"""
        # Create patient portal user if email provided
        if self.email:
            self.create_portal_user()
        
        # Send welcome SMS
        if self.mobile:
            self.send_welcome_sms()
    
    def create_portal_user(self):
        """Create portal user for patient"""
        if not frappe.db.exists("User", self.email):
            user = frappe.get_doc({
                "doctype": "User",
                "email": self.email,
                "first_name": self.first_name,
                "last_name": self.last_name,
                "send_welcome_email": 1,
                "user_type": "Website User"
            })
            user.insert(ignore_permissions=True)
            
            # Assign patient role
            user.add_roles("Patient")
    
    def send_welcome_sms(self):
        """Send welcome SMS to patient"""
        message = f"Welcome {self.first_name}! Your Patient ID is {self.name}."
        # Integrate with SMS gateway
        frappe.enqueue(
            method="hospital_management.utils.sms.send_sms",
            queue="short",
            mobile=self.mobile,
            message=message
        )
    
    @frappe.whitelist()
    def get_medical_history(self):
        """Get patient's complete medical history"""
        return {
            "consultations": frappe.get_all(
                "Consultation",
                filters={"patient": self.name},
                fields=["name", "date", "doctor", "diagnosis"],
                order_by="date desc",
                limit=10
            ),
            "prescriptions": frappe.get_all(
                "Prescription",
                filters={"patient": self.name},
                fields=["name", "date", "medicine", "dosage"],
                order_by="date desc"
            ),
            "allergies": [row.as_dict() for row in self.allergies],
            "chronic_conditions": [row.as_dict() for row in self.chronic_conditions]
        }
```

**Output:**
- Complete controller with validation
- Business logic implementation
- API methods
- Integration hooks

#### Phase 4: Frontend/Client Scripts

**Process:**
```
You: "Create client scripts for the Patient form"

Claude: [Generates client-side validation and UX enhancements]
```

**Generated Code:**
```javascript
// patient.js

frappe.ui.form.on('Patient', {
    refresh: function(frm) {
        // Add custom buttons
        if (!frm.doc.__islocal) {
            // Book Appointment button
            frm.add_custom_button(__('Book Appointment'), function() {
                book_appointment(frm);
            }, __('Actions'));
            
            // View Medical History button
            frm.add_custom_button(__('Medical History'), function() {
                view_medical_history(frm);
            }, __('View'));
            
            // Print ID Card button
            frm.add_custom_button(__('Print ID Card'), function() {
                print_id_card(frm);
            }, __('Print'));
        }
        
        // Set indicator based on insurance status
        if (frm.doc.has_insurance) {
            if (frm.doc.insurance_validity) {
                let today = frappe.datetime.get_today();
                let validity = frm.doc.insurance_validity;
                
                if (validity < today) {
                    frm.dashboard.add_indicator(__('Insurance Expired'), 'red');
                } else {
                    frm.dashboard.add_indicator(__('Insurance Active'), 'green');
                }
            }
        }
    },
    
    date_of_birth: function(frm) {
        // Auto-calculate age when DOB changes
        if (frm.doc.date_of_birth) {
            let age = frappe.datetime.get_diff(frappe.datetime.get_today(), 
                                               frm.doc.date_of_birth) / 365;
            frm.set_value('age', Math.floor(age));
        }
    },
    
    mobile: function(frm) {
        // Format mobile number
        if (frm.doc.mobile) {
            let mobile = frm.doc.mobile.replace(/\D/g, '');
            if (mobile.length === 10) {
                frm.set_value('mobile', mobile);
            }
        }
    },
    
    has_insurance: function(frm) {
        // Show/hide insurance fields
        frm.toggle_reqd(['insurance_provider', 'insurance_number'], 
                        frm.doc.has_insurance);
        frm.toggle_display('insurance_section', frm.doc.has_insurance);
    },
    
    email: function(frm) {
        // Check if user exists with this email
        if (frm.doc.email && !frm.doc.__islocal) {
            frappe.db.exists('User', frm.doc.email).then(exists => {
                if (!exists) {
                    frappe.msgprint({
                        title: __('Create Portal User'),
                        message: __('Would you like to create a portal user for this patient?'),
                        primary_action: {
                            label: __('Create User'),
                            action: function() {
                                create_portal_user(frm);
                            }
                        }
                    });
                }
            });
        }
    }
});

// Helper functions
function book_appointment(frm) {
    frappe.new_doc('Appointment', {
        patient: frm.doc.name,
        patient_name: frm.doc.full_name,
        patient_mobile: frm.doc.mobile
    });
}

function view_medical_history(frm) {
    frappe.call({
        method: 'get_medical_history',
        doc: frm.doc,
        callback: function(r) {
            if (r.message) {
                let d = new frappe.ui.Dialog({
                    title: __('Medical History - {0}', [frm.doc.full_name]),
                    fields: [
                        {
                            fieldtype: 'HTML',
                            fieldname: 'history_html'
                        }
                    ],
                    size: 'large'
                });
                
                let html = build_medical_history_html(r.message);
                d.fields_dict.history_html.$wrapper.html(html);
                d.show();
            }
        }
    });
}

function build_medical_history_html(data) {
    // Build formatted HTML for medical history
    let html = '<div class="medical-history">';
    
    // Consultations
    html += '<h4>Recent Consultations</h4>';
    if (data.consultations.length > 0) {
        html += '<table class="table table-bordered">';
        html += '<tr><th>Date</th><th>Doctor</th><th>Diagnosis</th></tr>';
        data.consultations.forEach(c => {
            html += `<tr>
                <td>${frappe.datetime.str_to_user(c.date)}</td>
                <td>${c.doctor}</td>
                <td>${c.diagnosis || '-'}</td>
            </tr>`;
        });
        html += '</table>';
    } else {
        html += '<p>No consultation history</p>';
    }
    
    // Allergies
    html += '<h4>Allergies</h4>';
    if (data.allergies.length > 0) {
        html += '<ul>';
        data.allergies.forEach(a => {
            html += `<li>${a.allergy_type} (${a.severity})</li>`;
        });
        html += '</ul>';
    } else {
        html += '<p>No known allergies</p>';
    }
    
    html += '</div>';
    return html;
}

function print_id_card(frm) {
    frappe.call({
        method: 'hospital_management.api.generate_patient_id_card',
        args: {
            patient: frm.doc.name
        },
        callback: function(r) {
            if (r.message) {
                window.open(r.message.url, '_blank');
            }
        }
    });
}

function create_portal_user(frm) {
    frappe.call({
        method: 'create_portal_user',
        doc: frm.doc,
        callback: function(r) {
            if (!r.exc) {
                frappe.show_alert(__('Portal user created successfully'));
                frm.reload_doc();
            }
        }
    });
}
```

#### Phase 5: API Development

**Process:**
```
You: "Create APIs for mobile app integration - patient login, 
     appointment booking, and medical history retrieval"

Claude: [Generates RESTful APIs with authentication]
```

**Generated Code:**
```python
# hospital_management/api.py

import frappe
from frappe import _
import json

@frappe.whitelist(allow_guest=True)
def patient_login(mobile, otp):
    """
    Authenticate patient using mobile and OTP
    
    Args:
        mobile: Patient mobile number
        otp: One-time password
    
    Returns:
        dict: Login success with patient details and token
    """
    # Verify OTP
    if not verify_otp(mobile, otp):
        frappe.throw(_("Invalid OTP"))
    
    # Get patient
    patient = frappe.db.get_value(
        "Patient",
        {"mobile": mobile},
        ["name", "full_name", "email"],
        as_dict=True
    )
    
    if not patient:
        frappe.throw(_("Patient not found"))
    
    # Generate auth token
    user = frappe.db.get_value("User", {"mobile_no": mobile}, "name")
    if user:
        token = frappe.generate_hash(length=32)
        frappe.db.set_value("User", user, "api_key", token)
        frappe.db.commit()
        
        return {
            "success": True,
            "patient": patient,
            "token": token,
            "message": _("Login successful")
        }
    else:
        frappe.throw(_("User account not found"))


@frappe.whitelist()
def book_appointment(patient, doctor, date, time_slot, symptoms=None):
    """
    Book an appointment
    
    Args:
        patient: Patient ID
        doctor: Doctor ID
        date: Appointment date
        time_slot: Time slot
        symptoms: Patient symptoms (optional)
    
    Returns:
        dict: Appointment details
    """
    # Validate inputs
    if not frappe.db.exists("Patient", patient):
        frappe.throw(_("Invalid patient"))
    
    if not frappe.db.exists("Doctor", doctor):
        frappe.throw(_("Invalid doctor"))
    
    # Check doctor availability
    is_available = check_doctor_availability(doctor, date, time_slot)
    if not is_available:
        frappe.throw(_("Doctor is not available at this time"))
    
    # Check if patient has pending appointments
    pending = frappe.db.count("Appointment", {
        "patient": patient,
        "status": ["in", ["Scheduled", "Draft"]],
        "date": [">=", frappe.utils.today()]
    })
    
    if pending >= 3:
        frappe.throw(_("You already have 3 pending appointments"))
    
    # Create appointment
    appointment = frappe.get_doc({
        "doctype": "Appointment",
        "patient": patient,
        "doctor": doctor,
        "appointment_date": date,
        "time_slot": time_slot,
        "symptoms": symptoms,
        "status": "Scheduled"
    })
    appointment.insert(ignore_permissions=True)
    
    # Send confirmation
    send_appointment_confirmation(appointment.name)
    
    frappe.db.commit()
    
    return {
        "success": True,
        "appointment_id": appointment.name,
        "message": _("Appointment booked successfully"),
        "details": appointment.as_dict()
    }


@frappe.whitelist()
def get_available_time_slots(doctor, date):
    """
    Get available time slots for a doctor on a specific date
    
    Args:
        doctor: Doctor ID
        date: Appointment date
    
    Returns:
        list: Available time slots
    """
    # Get doctor's schedule
    doctor_doc = frappe.get_doc("Doctor", doctor)
    
    # Get all appointments for the doctor on that date
    booked_slots = frappe.get_all(
        "Appointment",
        filters={
            "doctor": doctor,
            "appointment_date": date,
            "status": ["!=", "Cancelled"]
        },
        fields=["time_slot"]
    )
    
    booked_times = [slot.time_slot for slot in booked_slots]
    
    # Generate available slots based on doctor's schedule
    available_slots = []
    for schedule in doctor_doc.schedule:
        if schedule.day == frappe.utils.getdate(date).strftime("%A"):
            slots = generate_time_slots(
                schedule.from_time,
                schedule.to_time,
                doctor_doc.consultation_duration or 30
            )
            
            # Filter out booked slots
            available_slots.extend([
                slot for slot in slots if slot not in booked_times
            ])
    
    return {
        "success": True,
        "date": date,
        "doctor": doctor,
        "available_slots": available_slots
    }


@frappe.whitelist()
def get_patient_medical_history(patient):
    """
    Get complete medical history for a patient
    
    Args:
        patient: Patient ID
    
    Returns:
        dict: Complete medical history
    """
    # Verify patient access
    if not has_patient_access(patient):
        frappe.throw(_("Not authorized"), frappe.PermissionError)
    
    patient_doc = frappe.get_doc("Patient", patient)
    
    # Get consultations
    consultations = frappe.get_all(
        "Consultation",
        filters={"patient": patient, "docstatus": 1},
        fields=[
            "name", "date", "doctor", "diagnosis", 
            "treatment_plan", "follow_up_date"
        ],
        order_by="date desc"
    )
    
    # Get prescriptions
    prescriptions = frappe.get_all(
        "Prescription",
        filters={"patient": patient},
        fields=["name", "date", "medicines", "dosage", "duration"],
        order_by="date desc"
    )
    
    # Get lab results
    lab_results = frappe.get_all(
        "Lab Result",
        filters={"patient": patient},
        fields=["name", "test_date", "test_name", "result", "normal_range"],
        order_by="test_date desc"
    )
    
    return {
        "success": True,
        "patient_info": {
            "name": patient_doc.name,
            "full_name": patient_doc.full_name,
            "age": patient_doc.age,
            "blood_group": patient_doc.blood_group,
            "allergies": [row.as_dict() for row in patient_doc.allergies],
            "chronic_conditions": [row.as_dict() for row in patient_doc.chronic_conditions]
        },
        "consultations": consultations,
        "prescriptions": prescriptions,
        "lab_results": lab_results
    }


# Helper functions
def verify_otp(mobile, otp):
    """Verify OTP for mobile number"""
    # Implementation depends on your OTP service
    # This is a placeholder
    stored_otp = frappe.cache().get(f"otp_{mobile}")
    return stored_otp == otp


def check_doctor_availability(doctor, date, time_slot):
    """Check if doctor is available"""
    # Check if slot is already booked
    exists = frappe.db.exists("Appointment", {
        "doctor": doctor,
        "appointment_date": date,
        "time_slot": time_slot,
        "status": ["!=", "Cancelled"]
    })
    return not exists


def generate_time_slots(start_time, end_time, duration):
    """Generate time slots between start and end time"""
    from frappe.utils import time_diff_in_minutes, add_to_date
    import datetime
    
    slots = []
    current = start_time
    
    while time_diff_in_minutes(end_time, current) >= duration:
        slots.append(current.strftime("%H:%M"))
        current = add_to_date(current, minutes=duration)
    
    return slots


def send_appointment_confirmation(appointment_name):
    """Send appointment confirmation via SMS/Email"""
    appointment = frappe.get_doc("Appointment", appointment_name)
    patient = frappe.get_doc("Patient", appointment.patient)
    
    message = f"""
    Appointment Confirmed!
    Date: {frappe.utils.formatdate(appointment.appointment_date)}
    Time: {appointment.time_slot}
    Doctor: {appointment.doctor}
    ID: {appointment.name}
    """
    
    # Send SMS
    if patient.mobile:
        frappe.enqueue(
            method="hospital_management.utils.sms.send_sms",
            mobile=patient.mobile,
            message=message
        )
    
    # Send Email
    if patient.email:
        frappe.sendmail(
            recipients=[patient.email],
            subject="Appointment Confirmation",
            message=message
        )


def has_patient_access(patient):
    """Check if current user has access to patient records"""
    # Check if user is the patient
    user = frappe.session.user
    patient_user = frappe.db.get_value("Patient", patient, "email")
    
    if patient_user == user:
        return True
    
    # Check if user is a healthcare professional
    if "Healthcare Practitioner" in frappe.get_roles():
        return True
    
    return False
```

#### Phase 6: Testing

**Process:**
```
You: "Generate test cases for the Patient DocType"

Claude: [Creates comprehensive test suite]
```

**Generated Tests:**
```python
# test_patient.py

import frappe
import unittest
from frappe.utils import today, add_days, getdate

class TestPatient(unittest.TestCase):
    def setUp(self):
        """Set up test data"""
        self.test_patient_data = {
            "doctype": "Patient",
            "first_name": "John",
            "last_name": "Doe",
            "date_of_birth": add_days(today(), -365 * 30),  # 30 years old
            "gender": "Male",
            "mobile": "9876543210",
            "email": "john.doe@test.com",
            "blood_group": "O+"
        }
    
    def tearDown(self):
        """Clean up test data"""
        frappe.db.rollback()
    
    def test_patient_creation(self):
        """Test basic patient creation"""
        patient = frappe.get_doc(self.test_patient_data)
        patient.insert()
        
        self.assertTrue(patient.name)
        self.assertEqual(patient.full_name, "John Doe")
        self.assertEqual(patient.age, 30)
    
    def test_name_generation(self):
        """Test patient ID auto-generation"""
        patient = frappe.get_doc(self.test_patient_data)
        patient.insert()
        
        self.assertTrue(patient.name.startswith("PAT-"))
        self.assertTrue(len(patient.name) > 10)
    
    def test_age_calculation(self):
        """Test automatic age calculation"""
        patient = frappe.get_doc(self.test_patient_data)
        patient.date_of_birth = add_days(today(), -365 * 25)
        patient.insert()
        
        self.assertEqual(patient.age, 25)
    
    def test_future_dob_validation(self):
        """Test that future date of birth is not allowed"""
        patient = frappe.get_doc(self.test_patient_data)
        patient.date_of_birth = add_days(today(), 10)
        
        with self.assertRaises(frappe.ValidationError):
            patient.insert()
    
    def test_mobile_validation(self):
        """Test mobile number validation"""
        # Test invalid mobile
        patient = frappe.get_doc(self.test_patient_data)
        patient.mobile = "123"
        
        with self.assertRaises(frappe.ValidationError):
            patient.insert()
    
    def test_duplicate_mobile(self):
        """Test that duplicate mobile numbers are not allowed"""
        # Create first patient
        patient1 = frappe.get_doc(self.test_patient_data)
        patient1.insert()
        
        # Try to create second patient with same mobile
        patient2 = frappe.get_doc(self.test_patient_data)
        patient2.email = "different@test.com"
        
        with self.assertRaises(frappe.ValidationError):
            patient2.insert()
    
    def test_insurance_validation(self):
        """Test insurance field validation"""
        patient = frappe.get_doc(self.test_patient_data)
        patient.has_insurance = 1
        # Not providing required insurance fields
        
        with self.assertRaises(frappe.ValidationError):
            patient.insert()
    
    def test_medical_history_api(self):
        """Test get_medical_history API"""
        patient = frappe.get_doc(self.test_patient_data)
        patient.insert()
        
        history = patient.get_medical_history()
        
        self.assertIn('consultations', history)
        self.assertIn('prescriptions', history)
        self.assertIn('allergies', history)
```

#### Phase 7: Deployment

**Process:**
```
You: "The app is ready. Help me deploy to Frappe Cloud for production."

Claude: [Provides deployment checklist and instructions]
        
        DEPLOYMENT CHECKLIST:
        
        ✅ Pre-Deployment:
        - All tests passing
        - Code reviewed
        - Documentation complete
        - Demo data prepared
        
        📦 Frappe Cloud Deployment Steps:
        
        1. Push to GitHub:
           git add .
           git commit -m "Production ready"
           git push origin main
        
        2. Frappe Cloud Dashboard:
           - Go to your site
           - Apps → Install from GitHub
           - Enter: https://github.com/yourname/hospital_management
           - Select branch: main
           - Install
        
        3. Post-Deployment:
           - Run bench migrate
           - Test critical workflows
           - Monitor error logs
           - Set up scheduled backups
        
        Would you like help with any specific step?
```

### What Claude Can Do vs. What You Must Do

#### ✅ Claude Can Handle (with this skill):

1. **Requirements Analysis**
   - Break down BRD into modules
   - Identify DocTypes and relationships
   - Plan architecture
   - Suggest workflows

2. **Code Generation**
   - DocType controllers (Python)
   - Client scripts (JavaScript)
   - API endpoints
   - Validation logic
   - Business rules

3. **Test Creation**
   - Unit test scaffolding
   - Test case generation
   - Test data creation

4. **Documentation**
   - API documentation
   - User guides
   - Technical documentation

5. **Deployment Planning**
   - Platform selection guidance
   - Configuration recommendations
   - Migration strategies

#### ⚠️ You Must Still:

1. **Manual Steps in Frappe**
   - Create DocTypes via UI (in developer mode)
   - Configure permissions
   - Set up roles
   - Create workflows via UI
   - Design print formats

2. **Review & Refinement**
   - Code review
   - Logic validation
   - UX testing
   - Performance optimization

3. **Integration**
   - Set up actual payment gateways
   - Configure SMS/email services
   - Connect to external APIs

4. **Deployment**
   - Execute deployment commands
   - Monitor production
   - Handle incidents

5. **Business Decisions**
   - Final architecture approval
   - Feature prioritization
   - Platform selection
   - Security configurations

### Success Factors

For this to work effectively from BRD to deployment:

1. **Clear BRD** - Well-defined requirements
2. **Iterative Approach** - Build incrementally
3. **Continuous Testing** - Test at each phase
4. **Good Communication** - Clear prompts to Claude
5. **Developer Involvement** - Review AI-generated code
6. **Platform Knowledge** - Understanding of Frappe basics

### Realistic Timeline from BRD

**Simple App (10-15 DocTypes):** 2-4 weeks
**Medium App (20-30 DocTypes):** 1-2 months
**Complex App (40+ DocTypes):** 3-4 months

*With Claude + This Skill: 40-60% faster than manual development*

---

## Table of Contents

1. [Introduction](#introduction)
2. [Environment Setup](#environment-setup)
3. [Creating Your First App](#creating-your-first-app)
4. [Understanding App Architecture](#understanding-app-architecture)
5. [DocType Development](#doctype-development)
6. [Controller Methods & Hooks](#controller-methods--hooks)
7. [Frontend Development](#frontend-development)
8. [API Development](#api-development)
9. [Testing & Debugging](#testing--debugging)
10. [Deployment & Production](#deployment--production)
11. [Common Patterns & Best Practices](#common-patterns--best-practices)
12. [Troubleshooting Guide](#troubleshooting-guide)

---

## Introduction

### What is Frappe Framework?

Frappe is a full-stack web application framework written in Python and JavaScript. It's the foundation for ERPNext and many other business applications.

**Key Features:**
- **Full-Stack Framework**: Covers both backend (Python) and frontend (JavaScript)
- **Built-in Admin Interface**: Pre-built, customizable admin dashboard (Desk)
- **DocType System**: Powerful ORM for database modeling
- **Role-Based Permissions**: Comprehensive user and role management
- **Auto-generated REST API**: RESTful endpoints for all DocTypes
- **Real-time Updates**: WebSocket support via Socket.io
- **Background Jobs**: Redis-based job queue system
- **Reporting**: Built-in report builder and custom report support

### When to Build a Custom App

Build a custom Frappe app when you need:
- Business logic specific to your domain
- Custom data models not available in existing apps
- Integration with external systems
- Extending or modifying existing Frappe apps (like ERPNext, FCRM)
- Multi-tenant SaaS applications

---

## Environment Setup

### Prerequisites

**Required Knowledge:**
- Python (intermediate level)
- JavaScript (basic to intermediate)
- SQL/Database concepts
- Git version control
- Basic understanding of web technologies (HTTP, REST APIs)

**System Requirements:**
- Linux/macOS (WSL2 for Windows)
- Python 3.10+
- Node.js 18+
- MariaDB 10.6+ or PostgreSQL 13+
- Redis 6+
- Git

### Deployment Options Overview

Frappe apps can be developed and deployed on multiple platforms:

**1. Local Development (Recommended for Development):**
- Full control over environment
- Fastest iteration cycle
- Best for learning and testing
- Requires manual setup

**2. Frappe Cloud (Easiest for Production):**
- Managed hosting by Frappe
- Automatic updates and backups
- Built-in monitoring
- Easy scaling
- Developer mode available
- Best for: SaaS, business apps, production deployments

**3. Self-Hosted Cloud (AWS, DigitalOcean, etc.):**
- Full infrastructure control
- Custom scaling options
- More configuration required
- Best for: Enterprise deployments, custom infrastructure needs

**4. On-Premise Servers:**
- Maximum data control
- Air-gapped deployments possible
- Full customization
- Best for: Highly regulated industries, legacy integrations

### Local Development Setup

This section covers setting up a local development environment. Production deployments are covered in the Deployment section.

#### Installing Frappe Bench (Local)

Bench is the CLI tool to manage Frappe apps and sites.

```bash
# Install bench using pip
pip install frappe-bench

# Verify installation
bench --version

# Create a new bench (Frappe project directory)
bench init frappe-bench --frappe-branch version-15

# Navigate to bench directory
cd frappe-bench
```

**Directory Structure After Init:**
```
frappe-bench/
├── apps/           # All Frappe apps
│   └── frappe/    # Core Frappe app
├── sites/         # All sites (tenants)
├── config/        # Redis and other configs
├── env/           # Python virtual environment
├── logs/          # Application logs
└── Procfile       # Process definitions for production
```

### Creating a Development Site

```bash
# Create a new site
bench new-site mysite.local

# Set it as the default site (optional)
bench use mysite.local

# Start development server
bench start
```

Access your site at: `http://localhost:8000`

### Enabling Developer Mode

Developer mode is **essential** for custom app development. It enables:
- Creation of DocTypes via UI
- Automatic export of DocType JSON files
- Better debugging information
- Hot reloading of Python code

```bash
# Method 1: Via bench command
bench --site mysite.local set-config developer_mode 1

# Method 2: Direct file edit (sites/mysite.local/site_config.json)
{
  "developer_mode": 1
}

# Restart bench
bench restart
```

---

## Creating Your First App

### App Creation Command

```bash
# Navigate to your bench directory
cd frappe-bench

# Create new app
bench new-app library_management
```

**You'll be prompted for:**
- **App Title**: Library Management (display name)
- **App Description**: Library Management System
- **App Publisher**: Your Name/Company
- **App Email**: contact@example.com
- **App Icon**: octicon octicon-book (see https://primer.style/octicons/)
- **App Color**: blue
- **App License**: MIT

### Generated App Structure

```
apps/library_management/
├── config/                      # App configuration
│   ├── desktop.py              # Desktop icons
│   └── docs.py                 # Documentation settings
├── modules/                     # Your custom modules
│   └── Library Management/     # Default module
│   ├── modules.txt              # List of modules in app
│   ├── patches.txt              # Database migration patches
│   ├── public/                  # Static assets
│   │   ├── css/
│   │   ├── js/
│   │   └── dist/               # Built assets (auto-generated)
│   ├── templates/               # Jinja2 templates
│   │   ├── includes/           # Template includes
│   │   └── pages/              # Template pages
│   ├── www/                     # Web pages (portal)
│   ├── hooks.py                 # App hooks and configuration
│   ├── __init__.py
│   └── translations/            # Translation files
├── requirements.txt             # Python dependencies
├── pyproject.toml              # Python package config
├── package.json                # Node.js dependencies
├── README.md
└── license.txt
```

### Installing Your App on a Site

```bash
# Install app on your site
bench --site mysite.local install-app library_management

# Verify installation
bench --site mysite.local list-apps
# Output should show: frappe, library_management
```

### Understanding hooks.py

The `hooks.py` file is the central configuration for your app. It defines metadata and integration points.

**Example hooks.py:**

```python
from . import __version__ as app_version

# Basic App Info
app_name = "library_management"
app_title = "Library Management"
app_publisher = "Your Company"
app_description = "Library Management System"
app_email = "contact@yourcompany.com"
app_license = "MIT"
app_version = app_version

# App Dependencies (other Frappe apps)
required_apps = []  # e.g., ["erpnext", "hrms"]

# Document Events Hooks
doc_events = {
    "*": {
        "on_update": "library_management.api.on_any_doc_update",
    },
    "User": {
        "after_insert": "library_management.api.create_library_member",
    }
}

# Scheduled Tasks
scheduler_events = {
    "daily": [
        "library_management.tasks.daily.send_overdue_reminders"
    ],
    "hourly": [
        "library_management.tasks.hourly.sync_inventory"
    ],
    "cron": {
        "0 0 * * *": [  # Midnight every day
            "library_management.tasks.cleanup.archive_old_data"
        ]
    }
}

# Permissions
permission_query_conditions = {
    "Article": "library_management.permissions.article_query"
}

# Website/Portal
website_route_rules = [
    {"from_route": "/library/<path:path>", "to_route": "library"},
]

# Installation
before_install = "library_management.setup.before_install"
after_install = "library_management.setup.after_install"

# Fixtures (data to sync across sites)
fixtures = [
    "Custom Field",
    {"dt": "Role", "filters": [["name", "in", ["Librarian", "Library Member"]]]},
]

# Additional hooks available in official docs:
# - boot_session
# - override_whitelisted_methods
# - update_website_context
# - jenv (Jinja environment customization)
# - and many more...
```

---

## Understanding App Architecture

### The Module System

Modules organize DocTypes and features within your app. Think of them as feature folders.

**modules.txt:**
```
Library Management
Circulation
Reports
```

Each module gets its own directory:
```
library_management/
├── library_management/          # Module
├── circulation/                 # Module
└── reports/                     # Module
```

**Module Directory Structure:**
```
library_management/
├── doctype/                     # All DocTypes in this module
│   ├── article/
│   │   ├── article.py          # Controller
│   │   ├── article.js          # Client script
│   │   ├── article.json        # DocType definition
│   │   └── article_list.js     # List view customization
│   └── library_member/
├── page/                        # Custom pages
│   └── library_dashboard/
├── report/                      # Custom reports
│   ├── books_issued/
│   └── overdue_report/
└── __init__.py
```

### The MVC Pattern in Frappe

**Model (DocType JSON):**
- Defines database schema
- Field types, validations, permissions
- Stored as JSON in `doctype/*/doctype_name.json`

**Controller (Python):**
- Business logic
- Validation, calculations, workflows
- Stored as `doctype/*/doctype_name.py`

**View (JavaScript + HTML):**
- Client-side interactivity
- Form customizations, UI enhancements
- Stored as `doctype/*/doctype_name.js`

---

## DocType Development

### Creating a DocType

DocTypes are the fundamental data structures in Frappe.

**Via UI (Preferred in Dev Mode):**

1. Go to Desk → Search "New DocType"
2. Fill in basic info:
   - **Name**: Article
   - **Module**: Library Management
   - **Is Submittable**: Check if you need draft/submit workflow
   - **Is Child Table**: Check if this is a child DocType
   - **Track Changes**: Enable audit trail

3. Add fields (see field types below)
4. Set naming rules
5. Configure permissions
6. Save

**Automatically Generated Files:**
```
library_management/doctype/article/
├── __init__.py
├── article.py           # Controller (auto-generated)
├── article.json         # DocType definition (auto-generated)
└── test_article.py      # Unit test template
```

### Field Types

Common field types and their use cases:

| Field Type | Use Case | Example |
|------------|----------|---------|
| `Data` | Short text (max 140 chars) | Name, Email |
| `Text` | Long text | Description, Notes |
| `Select` | Dropdown with predefined options | Status, Priority |
| `Link` | Foreign key to another DocType | Customer, Item |
| `Table` | Child table (one-to-many) | Order Items, Addresses |
| `Check` | Boolean | Is Active, Is Published |
| `Date` | Date only | Birth Date, Due Date |
| `Datetime` | Date and time | Created At, Modified At |
| `Int` | Integer | Quantity, Age |
| `Float` | Decimal number | Price, Rating |
| `Currency` | Money with currency symbol | Amount, Total |
| `Attach` | File upload | Image, Document |
| `Attach Image` | Image with preview | Profile Picture |
| `Table MultiSelect` | Many-to-many relationship | Tags, Categories |
| `HTML` | Rich text editor | Article Content |
| `Code` | Code editor | Custom Script |
| `Dynamic Link` | Link to any DocType | Reference Document |
| `Read Only` | Computed/display field | Full Name, Total Amount |

### DocType Configuration Options

**Important Checkbox Options:**

| Option | Description |
|--------|-------------|
| **Is Submittable** | Enables draft→submit→cancel workflow. Required for transactions. |
| **Track Changes** | Maintains version history of all changes |
| **Is Child Table** | Makes this a child DocType (cannot exist independently) |
| **Is Single** | Only one record exists (e.g., Settings DocTypes) |
| **Is Virtual** | No database table (data from external source) |
| **Quick Entry** | Enables quick add dialog |
| **Track Seen** | Tracks who viewed the document |
| **Allow Rename** | Allows users to rename documents |
| **Read Only** | No modifications allowed after creation |
| **In Create** | Show in "New" menu |

**Naming Rules:**

```python
# In DocType settings:
# 1. Auto-name field
autoname = "field:title"  # Use title field as document name

# 2. Naming Series
naming_series = "LIB-ART-.YYYY.-.#####"  # LIB-ART-2024-00001

# 3. Custom (define in controller)
# See controller section below
```

### Child DocTypes

Child DocTypes represent one-to-many relationships.

**Example: Order → Order Items**

**Parent DocType (Sales Order):**
- Has a Table field pointing to child DocType

**Child DocType (Sales Order Item):**
- Check "Is Child Table"
- Will appear as a table in parent form

```python
# Parent Controller
class SalesOrder(Document):
    def validate(self):
        total = 0
        for item in self.items:  # Access child table
            item.amount = item.qty * item.rate
            total += item.amount
        self.total = total
```

### Single DocTypes

Single DocTypes have only one record (like application settings).

```python
# Example: Library Settings
{
    "name": "Library Settings",
    "is_single": 1,
    "fields": [
        {"fieldname": "max_books_per_member", "fieldtype": "Int"},
        {"fieldname": "late_fee_per_day", "fieldtype": "Currency"}
    ]
}
```

Access in code:
```python
settings = frappe.get_single("Library Settings")
max_books = settings.max_books_per_member
```

---

## Controller Methods & Hooks

### Basic Controller Structure

```python
# library_management/doctype/article/article.py

import frappe
from frappe.model.document import Document

class Article(Document):
    # Custom methods
    def get_full_title(self):
        """Returns formatted title with author"""
        return f"{self.title} by {self.author}"
    
    # Controller hooks (lifecycle methods)
    def before_insert(self):
        """Called before inserting into database"""
        # Useful for: Setting defaults, initial calculations
        if not self.status:
            self.status = "Available"
    
    def after_insert(self):
        """Called after inserting into database"""
        # Useful for: Triggering notifications, creating linked docs
        self.create_barcode()
    
    def validate(self):
        """Called before saving (insert or update)"""
        # Useful for: Validations, business logic, calculations
        self.validate_isbn()
        self.calculate_total_pages()
    
    def before_save(self):
        """Called before saving to database"""
        # Useful for: Last-minute modifications
        self.modified_by_custom = frappe.session.user
    
    def on_update(self):
        """Called after saving to database"""
        # Useful for: Syncing data, updating related records
        self.update_inventory()
    
    def before_submit(self):
        """Called before submit (if Is Submittable)"""
        self.validate_can_submit()
    
    def on_submit(self):
        """Called after submit"""
        self.create_ledger_entry()
    
    def before_cancel(self):
        """Called before cancel"""
        self.check_if_can_cancel()
    
    def on_cancel(self):
        """Called after cancel"""
        self.reverse_ledger_entry()
    
    def on_trash(self):
        """Called before deletion"""
        # Useful for: Cleanup, deleting related docs
        self.delete_related_records()
    
    def after_delete(self):
        """Called after deletion"""
        pass
    
    # Custom validation method
    def validate_isbn(self):
        if self.isbn and len(self.isbn) not in [10, 13]:
            frappe.throw("ISBN must be 10 or 13 characters")
    
    def calculate_total_pages(self):
        # Custom calculation
        if self.chapters:
            self.total_pages = sum([c.pages for c in self.chapters])
```

### Complete Controller Hook Reference

**Hook Execution Order:**

```
INSERT:
1. before_insert
2. validate
3. before_save
4. [DATABASE INSERT]
5. after_insert
6. on_update
7. after_save

UPDATE:
1. validate
2. before_save
3. [DATABASE UPDATE]
4. on_update
5. after_save

SUBMIT (if Is Submittable):
1. validate
2. before_submit
3. before_save
4. [DATABASE UPDATE - docstatus = 1]
5. on_submit
6. on_update
7. after_save

CANCEL (if Is Submittable):
1. before_cancel
2. [DATABASE UPDATE - docstatus = 2]
3. on_cancel
4. on_update

DELETE:
1. on_trash
2. [DATABASE DELETE]
3. after_delete
```

### Using frappe.throw for Validation

```python
def validate(self):
    # Simple error
    if self.age < 18:
        frappe.throw("Age must be at least 18")
    
    # Error with title
    if not self.email:
        frappe.throw("Email is required", title="Missing Information")
    
    # Multiple errors
    errors = []
    if not self.first_name:
        errors.append("First Name is required")
    if not self.last_name:
        errors.append("Last Name is required")
    
    if errors:
        frappe.throw("<br>".join(errors), title="Validation Error")
    
    # Custom exception type
    if duplicate:
        frappe.throw("Duplicate entry found", 
                    frappe.DuplicateEntryError)
```

### Global Hooks (hooks.py)

Hook into ANY DocType's lifecycle from your app:

```python
# hooks.py
doc_events = {
    # All DocTypes
    "*": {
        "after_insert": "library_management.api.log_all_inserts",
        "on_update": "library_management.api.sync_to_external_system"
    },
    
    # Specific DocType
    "User": {
        "after_insert": "library_management.api.create_library_member_on_user_create",
        "validate": "library_management.api.validate_user_email"
    },
    
    # Multiple hooks for same event
    "Sales Invoice": {
        "on_submit": [
            "library_management.api.update_inventory",
            "library_management.api.create_accounting_entry",
            "library_management.api.send_notification"
        ]
    }
}
```

**Hook function signature:**
```python
# library_management/api.py
def log_all_inserts(doc, method=None):
    """
    doc: The document object
    method: The hook method name (e.g., 'after_insert')
    """
    frappe.logger().info(f"New {doc.doctype} inserted: {doc.name}")
```

### Custom Naming

```python
# In controller
class Article(Document):
    def autoname(self):
        # Custom naming logic
        self.name = f"ART-{self.isbn}-{frappe.utils.nowdate()}"
        
        # Or use Frappe's naming utilities
        from frappe.model.naming import make_autoname
        self.name = make_autoname("LIB-.article_type.-.YYYY.-.#####")
```

---

## Frontend Development

### Client Scripts (DocType.js)

Client scripts add interactivity to forms.

```javascript
// article.js

frappe.ui.form.on('Article', {
    // Form initialization
    onload: function(frm) {
        // Runs when form loads
        console.log('Form loaded');
    },
    
    refresh: function(frm) {
        // Runs every time form is displayed
        // Add custom buttons
        if (frm.doc.status === 'Available') {
            frm.add_custom_button('Issue Book', function() {
                // Custom action
                frappe.prompt({
                    label: 'Member',
                    fieldname: 'member',
                    fieldtype: 'Link',
                    options: 'Library Member',
                    reqd: 1
                }, function(values) {
                    issue_book(frm, values.member);
                }, 'Issue Book to Member');
            });
        }
        
        // Modify field properties
        if (frm.doc.__islocal) {  // New document
            frm.set_value('status', 'Draft');
        }
        
        // Set field as read-only
        frm.set_df_property('isbn', 'read_only', frm.doc.docstatus === 1);
        
        // Hide/show fields
        frm.toggle_display('internal_notes', frappe.user.has_role('Librarian'));
    },
    
    // Field change events
    isbn: function(frm) {
        // Triggered when ISBN field changes
        if (frm.doc.isbn) {
            fetch_book_details(frm);
        }
    },
    
    author: function(frm) {
        // Auto-set publisher based on author
        if (frm.doc.author) {
            frappe.db.get_value('Author', frm.doc.author, 'default_publisher')
                .then(r => {
                    if (r.message.default_publisher) {
                        frm.set_value('publisher', r.message.default_publisher);
                    }
                });
        }
    },
    
    before_save: function(frm) {
        // Client-side validation before save
        if (frm.doc.price < 0) {
            frappe.throw('Price cannot be negative');
        }
    },
    
    after_save: function(frm) {
        // After successful save
        frappe.show_alert({
            message: 'Article saved successfully',
            indicator: 'green'
        }, 5);
    }
});

// Helper functions
function fetch_book_details(frm) {
    frappe.call({
        method: 'library_management.api.get_book_by_isbn',
        args: {
            isbn: frm.doc.isbn
        },
        callback: function(r) {
            if (r.message) {
                frm.set_value('title', r.message.title);
                frm.set_value('author', r.message.author);
                frm.set_value('publisher', r.message.publisher);
            }
        }
    });
}

function issue_book(frm, member) {
    frappe.call({
        method: 'library_management.api.issue_book',
        args: {
            article: frm.doc.name,
            member: member
        },
        callback: function(r) {
            if (!r.exc) {
                frm.reload_doc();
                frappe.show_alert('Book issued successfully');
            }
        }
    });
}
```

### Common Client Script Patterns

**1. Dynamic Filters:**
```javascript
frappe.ui.form.on('Article', {
    publisher: function(frm) {
        // Filter authors by publisher
        frm.set_query('author', function() {
            return {
                filters: {
                    publisher: frm.doc.publisher
                }
            };
        });
    }
});
```

**2. Fetch Values from Linked DocType:**
```javascript
frappe.ui.form.on('Library Transaction', {
    member: function(frm) {
        if (frm.doc.member) {
            frappe.db.get_value('Library Member', frm.doc.member, 
                ['email', 'phone', 'membership_type'])
                .then(r => {
                    let data = r.message;
                    frm.set_value('member_email', data.email);
                    frm.set_value('member_phone', data.phone);
                    frm.set_value('membership_type', data.membership_type);
                });
        }
    }
});
```

**3. Child Table Operations:**
```javascript
frappe.ui.form.on('Order Item', {  // Child DocType
    qty: function(frm, cdt, cdn) {
        // cdt = child doctype, cdn = child doc name
        let row = locals[cdt][cdn];
        frappe.model.set_value(cdt, cdn, 'amount', 
            row.qty * row.rate);
    },
    
    items_add: function(frm, cdt, cdn) {
        // When a new row is added to items table
        let row = locals[cdt][cdn];
        frappe.model.set_value(cdt, cdn, 'qty', 1);
    },
    
    items_remove: function(frm, cdt, cdn) {
        // When a row is removed
        calculate_total(frm);
    }
});

function calculate_total(frm) {
    let total = 0;
    frm.doc.items.forEach(item => {
        total += item.amount;
    });
    frm.set_value('total', total);
}
```

**4. Custom Dialogs:**
```javascript
function show_custom_dialog(frm) {
    let d = new frappe.ui.Dialog({
        title: 'Enter Details',
        fields: [
            {
                label: 'Member',
                fieldname: 'member',
                fieldtype: 'Link',
                options: 'Library Member',
                reqd: 1
            },
            {
                label: 'Issue Date',
                fieldname: 'issue_date',
                fieldtype: 'Date',
                default: frappe.datetime.get_today()
            },
            {
                label: 'Notes',
                fieldname: 'notes',
                fieldtype: 'Text'
            }
        ],
        primary_action_label: 'Submit',
        primary_action: function(values) {
            // Process values
            console.log(values);
            d.hide();
        }
    });
    d.show();
}
```

### List View Customization

```javascript
// article_list.js

frappe.listview_settings['Article'] = {
    // Add custom button in list view
    onload: function(listview) {
        listview.page.add_inner_button('Import Books', function() {
            // Custom action
        });
    },
    
    // Add indicator color
    get_indicator: function(doc) {
        if (doc.status === 'Issued') {
            return [__('Issued'), 'orange', 'status,=,Issued'];
        } else if (doc.status === 'Available') {
            return [__('Available'), 'green', 'status,=,Available'];
        } else {
            return [__('Unavailable'), 'red', 'status,=,Unavailable'];
        }
    },
    
    // Format how fields are displayed
    formatters: {
        title: function(value, df, doc) {
            return `<strong>${value}</strong>`;
        },
        isbn: function(value) {
            return value ? `ISBN: ${value}` : '';
        }
    },
    
    // Add custom columns
    add_fields: ['isbn', 'author', 'publisher'],
    
    // Hide standard columns
    hide_name_column: false
};
```

---

## API Development

### Whitelisted Methods (@frappe.whitelist)

Create server-side APIs that can be called from client or external systems.

```python
# library_management/api.py

import frappe
from frappe import _

@frappe.whitelist()
def get_book_by_isbn(isbn):
    """
    Get book details by ISBN from external API
    
    Args:
        isbn (str): ISBN number
    
    Returns:
        dict: Book details
    """
    # This function is accessible via:
    # POST /api/method/library_management.api.get_book_by_isbn
    
    # Validate input
    if not isbn:
        frappe.throw(_("ISBN is required"))
    
    # Your logic here
    book = frappe.db.get_value(
        "Article",
        {"isbn": isbn},
        ["title", "author", "publisher"],
        as_dict=True
    )
    
    if not book:
        frappe.throw(_("Book not found"))
    
    return book


@frappe.whitelist()
def issue_book(article, member):
    """Issue a book to a member"""
    # Check permissions
    if not frappe.has_permission("Library Transaction", "create"):
        frappe.throw(_("Not permitted"), frappe.PermissionError)
    
    # Validate
    article_doc = frappe.get_doc("Article", article)
    if article_doc.status != "Available":
        frappe.throw(_("Article is not available"))
    
    member_doc = frappe.get_doc("Library Member", member)
    if member_doc.outstanding_books >= 5:
        frappe.throw(_("Member has too many outstanding books"))
    
    # Create transaction
    transaction = frappe.get_doc({
        "doctype": "Library Transaction",
        "article": article,
        "member": member,
        "type": "Issue",
        "date": frappe.utils.nowdate()
    })
    transaction.insert()
    
    # Update article status
    article_doc.status = "Issued"
    article_doc.save()
    
    frappe.db.commit()
    
    return transaction.name


@frappe.whitelist(allow_guest=True)
def search_books(query):
    """Public API - allows guest access"""
    return frappe.db.sql("""
        SELECT name, title, author, isbn
        FROM `tabArticle`
        WHERE status = 'Available'
        AND (title LIKE %(query)s OR author LIKE %(query)s)
        LIMIT 20
    """, {"query": f"%{query}%"}, as_dict=True)


@frappe.whitelist(methods=['POST'])
def bulk_import_books(books):
    """Only allow POST method"""
    # books is JSON array
    import json
    books = json.loads(books) if isinstance(books, str) else books
    
    created = []
    for book in books:
        try:
            doc = frappe.get_doc({
                "doctype": "Article",
                "title": book.get("title"),
                "author": book.get("author"),
                "isbn": book.get("isbn")
            })
            doc.insert()
            created.append(doc.name)
        except Exception as e:
            frappe.log_error(f"Failed to import book: {str(e)}")
    
    frappe.db.commit()
    return {"success": True, "created": created}
```

### Calling APIs from JavaScript

```javascript
// Call whitelisted method
frappe.call({
    method: 'library_management.api.issue_book',
    args: {
        article: 'ART-001',
        member: 'MEM-001'
    },
    callback: function(r) {
        if (r.message) {
            console.log('Transaction:', r.message);
        }
    },
    error: function(r) {
        console.error('Error:', r);
    }
});

// With freeze (shows loading indicator)
frappe.call({
    method: 'library_management.api.bulk_import_books',
    args: {
        books: [{title: 'Book 1'}, {title: 'Book 2'}]
    },
    freeze: true,
    freeze_message: 'Importing books...',
    callback: function(r) {
        frappe.show_alert('Import complete');
    }
});
```

### REST API (Auto-generated)

Frappe automatically creates REST endpoints for all DocTypes.

**Authentication:**
```bash
# Generate API Key and Secret
# Desk → User → API Access → Generate Keys

# Using Basic Auth
curl -X GET "https://yoursite.com/api/resource/Article" \
  -u "api_key:api_secret"

# Using Token
curl -X GET "https://yoursite.com/api/resource/Article" \
  -H "Authorization: token api_key:api_secret"
```

**CRUD Operations:**
```bash
# GET - List documents
GET /api/resource/Article
GET /api/resource/Article?fields=["title","author"]&limit=10

# GET - Single document
GET /api/resource/Article/ART-001

# POST - Create document
POST /api/resource/Article
Content-Type: application/json
{
    "title": "New Book",
    "author": "John Doe",
    "isbn": "1234567890"
}

# PUT - Update document
PUT /api/resource/Article/ART-001
Content-Type: application/json
{
    "status": "Issued"
}

# DELETE - Delete document
DELETE /api/resource/Article/ART-001
```

### Database Queries

```python
# Get single value
title = frappe.db.get_value("Article", "ART-001", "title")

# Get multiple values
title, author = frappe.db.get_value(
    "Article", "ART-001", ["title", "author"]
)

# Get as dict
article = frappe.db.get_value(
    "Article", "ART-001", 
    ["title", "author", "isbn"], 
    as_dict=True
)

# Get with filters
article = frappe.db.get_value(
    "Article",
    {"isbn": "1234567890", "status": "Available"},
    ["name", "title"]
)

# Get all matching documents
articles = frappe.db.get_all(
    "Article",
    filters={"author": "John Doe", "status": "Available"},
    fields=["name", "title", "isbn"],
    order_by="creation desc",
    limit=20
)

# Count
count = frappe.db.count("Article", {"status": "Available"})

# SQL query (use sparingly)
results = frappe.db.sql("""
    SELECT a.name, a.title, COUNT(t.name) as issue_count
    FROM `tabArticle` a
    LEFT JOIN `tabLibrary Transaction` t ON t.article = a.name
    WHERE a.status = %(status)s
    GROUP BY a.name
    ORDER BY issue_count DESC
    LIMIT 10
""", {"status": "Available"}, as_dict=True)

# Insert/Update
frappe.db.set_value("Article", "ART-001", "status", "Available")
frappe.db.set_value("Article", "ART-001", {
    "status": "Available",
    "location": "Shelf A1"
})

# Commit changes
frappe.db.commit()

# Rollback
frappe.db.rollback()
```

---

## Testing & Debugging

### Unit Testing

Every DocType gets a test file:

```python
# library_management/doctype/article/test_article.py

import frappe
import unittest

class TestArticle(unittest.TestCase):
    def setUp(self):
        """Set up test data"""
        self.test_article = frappe.get_doc({
            "doctype": "Article",
            "title": "Test Book",
            "author": "Test Author",
            "isbn": "1234567890"
        })
        self.test_article.insert()
    
    def tearDown(self):
        """Clean up after test"""
        frappe.delete_doc("Article", self.test_article.name, force=True)
    
    def test_article_creation(self):
        """Test basic article creation"""
        self.assertTrue(self.test_article.name)
        self.assertEqual(self.test_article.status, "Available")
    
    def test_isbn_validation(self):
        """Test ISBN validation"""
        article = frappe.get_doc({
            "doctype": "Article",
            "title": "Invalid ISBN Book",
            "isbn": "123"  # Invalid
        })
        
        with self.assertRaises(frappe.ValidationError):
            article.insert()
    
    def test_issue_book(self):
        """Test issuing a book"""
        from library_management.api import issue_book
        
        member = frappe.get_doc({
            "doctype": "Library Member",
            "first_name": "Test",
            "last_name": "Member",
            "email": "test@example.com"
        }).insert()
        
        transaction_name = issue_book(self.test_article.name, member.name)
        
        # Verify
        self.assertTrue(transaction_name)
        self.test_article.reload()
        self.assertEqual(self.test_article.status, "Issued")
        
        # Cleanup
        frappe.delete_doc("Library Member", member.name, force=True)

# Run tests:
# bench --site mysite.local run-tests --app library_management
# bench --site mysite.local run-tests --doctype "Article"
```

### Debugging

**1. Console Logging:**
```python
# Python
import frappe
frappe.logger().info(f"Article: {article.name}")
frappe.logger().error("Something went wrong")

# Access logs
tail -f sites/mysite.local/logs/site.log
```

**2. Error Logging:**
```python
try:
    # Risky operation
    process_data()
except Exception as e:
    frappe.log_error(
        title="Data Processing Error",
        message=frappe.get_traceback()
    )
```

**3. Debug Mode:**
```python
# Add to any method
import pdb; pdb.set_trace()

# Or use Frappe's debug
frappe.errprint("Debug value:", some_variable)
```

**4. Bench Console:**
```bash
bench --site mysite.local console

# Interactive Python shell with Frappe context
>>> doc = frappe.get_doc("Article", "ART-001")
>>> doc.title
'Sample Book'
```

### Performance Profiling

```python
# In hooks.py
profiler_allowed = True  # Enable profiler in developer mode

# Add ?_p=1 to any URL to profile
# View results in logs/profiler/
```

---

## Deployment & Production

### Deployment Strategies by Platform

#### Option 1: Frappe Cloud (Recommended for Most Users)

**Pros:**
- Zero infrastructure management
- Automatic backups and updates
- Built-in monitoring and alerts
- Easy scaling
- Developer mode available
- One-click deployments

**Steps:**
1. Create account at https://frappecloud.com
2. Create a new site
3. Install your app from GitHub:
   - Connect your GitHub repository
   - Select branch to deploy
   - Frappe Cloud builds and installs automatically
4. Configure domain and SSL (automatic)

**App Deployment on Frappe Cloud:**
```bash
# Push your app to GitHub
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/yourusername/your_app.git
git push -u origin main

# In Frappe Cloud dashboard:
# 1. Go to your site
# 2. Apps → Install App → From GitHub
# 3. Enter repository URL
# 4. Select branch
# 5. Install
```

**Developer Mode on Frappe Cloud:**
```bash
# Enable via dashboard or SSH
bench --site yoursite.com set-config developer_mode 1
```

#### Option 2: Self-Hosted on Cloud (AWS, DigitalOcean, etc.)

**Suitable for:**
- Custom infrastructure requirements
- Integration with existing cloud services
- Advanced networking needs
- Cost optimization for large deployments

**AWS EC2 Setup:**
```bash
# Launch EC2 instance (Ubuntu 22.04 LTS, t3.medium or larger)
# SSH into instance
ssh -i your-key.pem ubuntu@your-ec2-ip

# Install dependencies
sudo apt update && sudo apt upgrade -y
sudo apt install git python3-dev python3-pip redis-server mariadb-server -y

# Install Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# Install Bench
sudo pip3 install frappe-bench

# Create bench
bench init frappe-bench --frappe-branch version-15
cd frappe-bench

# Create site
bench new-site yoursite.com

# Setup production
sudo bench setup production $USER
```

**DigitalOcean Droplet Setup:**
Similar to AWS, but you can also use their Marketplace:
1. Create a droplet from "Frappe" Marketplace image
2. SSH and configure
3. Deploy your app

**Security Checklist for Cloud:**
- [ ] Configure firewall (allow only 80, 443, 22)
- [ ] Setup SSH key authentication
- [ ] Disable root login
- [ ] Configure automatic security updates
- [ ] Setup monitoring (CloudWatch, DigitalOcean Monitoring)
- [ ] Configure backups
- [ ] Setup SSL certificates

#### Option 3: On-Premise Servers

**Suitable for:**
- Data sovereignty requirements
- Air-gapped environments
- Legacy system integration
- Regulatory compliance

**Setup Process:**
```bash
# Same as cloud setup, but on your hardware
# Additional considerations:
# - UPS backup power
# - Physical security
# - Network configuration
# - Backup infrastructure
```

### Preparing for Production (All Platforms)

**1. Disable Developer Mode:**
```bash
bench --site yoursite.com set-config developer_mode 0
bench restart
```

**2. Build Assets:**
```bash
# Build your app's frontend assets
bench build --app your_app_name

# Build all assets
bench build
```

**3. Database Migrations:**

Create migration patches when you modify DocTypes in production:

```python
# your_app/patches.txt
your_app.patches.v1_0.update_article_status
your_app.patches.v1_0.migrate_old_data

# your_app/patches/v1_0/update_article_status.py
import frappe

def execute():
    """Migration script"""
    frappe.db.sql("""
        UPDATE `tabArticle`
        SET status = 'Available'
        WHERE status IS NULL
    """)
    frappe.db.commit()
```

Run migrations:
```bash
bench --site yoursite.com migrate
```

### Production Setup (Self-Hosted Only)

**For Frappe Cloud users:** Skip this section - production setup is automatic.

**For Self-Hosted deployments:**

**1. Setup Production Services:**
```bash
# Install and configure nginx, supervisor
sudo bench setup production $USER

# Enable SSL (Let's Encrypt)
sudo bench setup lets-encrypt yoursite.com

# Reload services
sudo supervisorctl reload
sudo service nginx reload
```

**2. Configure Supervisor:**
```bash
# Check supervisor status
sudo supervisorctl status

# Restart all processes
sudo supervisorctl restart all

# View logs
sudo supervisorctl tail -f frappe-bench-web:
```

**3. Scheduler Setup:**
```bash
# Enable background jobs
bench --site yoursite.com enable-scheduler
bench --site yoursite.com scheduler status

# Scheduler runs automatically via supervisor
```

**4. Environment Configuration:**
```json
// sites/yoursite.com/site_config.json
{
  "db_name": "production_db",
  "db_password": "secure_password",
  "developer_mode": 0,
  "maintenance_mode": 0,
  "limits": {
    "emails_per_day": 1000,
    "space_limit": 5000  // MB
  },
  "monitor": 1  // Enable monitoring
}
```

### Backup & Restore (All Platforms)

**Frappe Cloud:**
- Automatic daily backups
- Manual backups via dashboard
- Download backups for local storage
- Point-in-time restore available

**Self-Hosted:**
```bash
# Manual backup
bench --site yoursite.com backup
bench --site yoursite.com backup --with-files

# Restore
bench --site yoursite.com restore /path/to/backup.sql.gz
bench --site yoursite.com restore --with-private-files /path/to/files.tar
bench --site yoursite.com restore --with-public-files /path/to/public-files.tar

# Automated backups (configure in site_config.json)
{
  "backup_limit": 3,  // Keep last 3 backups
  "enable_auto_backup": 1,  // Daily automatic backups
  "backup_path": "/path/to/backups"  // Custom backup location
}

# Setup S3 backups (optional)
{
  "backup_to_s3": 1,
  "s3_backup_bucket": "your-bucket-name",
  "aws_access_key_id": "your-access-key",
  "aws_secret_access_key": "your-secret-key"
}
```

### Monitoring & Logging

**Frappe Cloud:**
- Built-in monitoring dashboard
- Automatic alerts
- Performance metrics
- Error tracking

**Self-Hosted:**

**1. Application Logs:**
```bash
# Site-specific errors
tail -f sites/yoursite.com/logs/error.log

# General bench logs
tail -f logs/bench.log

# Background job logs
tail -f logs/worker.log

# Web server logs
tail -f logs/web.log
```

**2. System Monitoring:**
```bash
# Install monitoring tools
sudo apt install htop iotop nethogs

# Check resource usage
htop
df -h  # Disk usage
free -h  # Memory usage

# Database size
bench --site yoursite.com mariadb
> SELECT table_schema, SUM(data_length + index_length) / 1024 / 1024 AS "Size (MB)" 
  FROM information_schema.tables GROUP BY table_schema;
```

**3. Error Tracking:**
- Use Frappe's built-in Error Log DocType
- Configure email notifications for errors
- Optional: Integrate with Sentry, Rollbar

### Scaling Strategies

**Vertical Scaling (Single Server):**
- Increase CPU/RAM
- Optimize database
- Add Redis caching
- Enable CDN for static files

**Horizontal Scaling (Multiple Servers):**
```
┌─────────────┐
│ Load        │
│ Balancer    │
└──────┬──────┘
       │
   ┌───┴───┐
   │       │
┌──▼──┐ ┌──▼──┐
│Web  │ │Web  │  (Multiple application servers)
│Node │ │Node │
└──┬──┘ └──┬──┘
   │       │
   └───┬───┘
       │
┌──────▼──────┐
│  Database   │  (Single or replicated)
│  Server     │
└─────────────┘
```

**Setup Multi-Server:**
1. Setup separate database server
2. Configure multiple app servers
3. Use common file storage (NFS/S3)
4. Setup load balancer (nginx/HAProxy)
5. Configure Redis for session sharing

### SSL/TLS Configuration

**Frappe Cloud:**
- Automatic SSL with Let's Encrypt
- Custom SSL certificate support
- Auto-renewal

**Self-Hosted:**
```bash
# Let's Encrypt (Free)
sudo bench setup lets-encrypt yoursite.com

# Custom SSL Certificate
# 1. Obtain certificate from CA
# 2. Place in /etc/nginx/ssl/
# 3. Configure nginx manually
```

### Performance Optimization

**1. Database:**
```bash
# Add indexes for frequently queried fields
bench --site yoursite.com mariadb
> ALTER TABLE `tabArticle` ADD INDEX idx_status (status);

# Optimize tables
> OPTIMIZE TABLE `tabArticle`;

# Configure MariaDB for production
# Edit /etc/mysql/mariadb.conf.d/50-server.cnf
innodb_buffer_pool_size = 4G  # 70-80% of available RAM
max_connections = 200
```

**2. Redis Caching:**
```bash
# Configure Redis limits
# Edit /etc/redis/redis.conf
maxmemory 2gb
maxmemory-policy allkeys-lru
```

**3. Application Optimization:**
```python
# Use caching
@frappe.cache(ttl=3600)
def get_expensive_data():
    return expensive_calculation()

# Optimize queries
# Bad: N+1 queries
for item in items:
    supplier = frappe.get_doc("Supplier", item.supplier)

# Good: Single query
suppliers = frappe.get_all(
    "Supplier",
    filters={"name": ["in", [i.supplier for i in items]]},
    fields=["name", "supplier_name"]
)
```

**4. Static Asset CDN:**
```json
// site_config.json
{
  "cdn_url": "https://cdn.yoursite.com",
  "serve_static_files": 0
}
```

### Deployment Checklist

**Pre-Deployment:**
- [ ] All tests passing
- [ ] Code reviewed
- [ ] Database migrations tested
- [ ] Backup current production
- [ ] Schedule maintenance window
- [ ] Notify users (if applicable)

**Deployment:**
- [ ] Pull latest code
- [ ] Run migrations: `bench migrate`
- [ ] Build assets: `bench build`
- [ ] Clear cache: `bench clear-cache`
- [ ] Restart services
- [ ] Verify deployment

**Post-Deployment:**
- [ ] Check error logs
- [ ] Verify critical functionality
- [ ] Monitor performance
- [ ] Rollback plan ready

### Rollback Procedure

```bash
# If deployment fails, rollback:

# 1. Stop services
sudo supervisorctl stop all

# 2. Restore database backup
bench --site yoursite.com restore /path/to/backup.sql.gz

# 3. Checkout previous code version
git checkout previous-stable-tag

# 4. Build assets
bench build

# 5. Restart services
sudo supervisorctl start all

# 6. Verify rollback
bench --site yoursite.com doctor
```

### CI/CD Integration

**GitHub Actions Example:**
```yaml
# .github/workflows/deploy.yml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - name: Run Tests
        run: |
          bench run-tests --app your_app
      
      - name: Deploy to Frappe Cloud
        if: github.ref == 'refs/heads/main'
        run: |
          # Frappe Cloud auto-deploys from GitHub
          echo "Deployed via Frappe Cloud"
      
      # For self-hosted:
      - name: Deploy to Server
        if: github.ref == 'refs/heads/main'
        run: |
          ssh user@server 'cd frappe-bench && git pull && bench migrate && bench build && sudo supervisorctl restart all'
```

---

## Common Patterns & Best Practices

### 1. Permission Checks

```python
@frappe.whitelist()
def sensitive_operation(doc_name):
    # Check permission
    if not frappe.has_permission("Article", "write", doc_name):
        frappe.throw(_("Not permitted"), frappe.PermissionError)
    
    # Check role
    if "Librarian" not in frappe.get_roles():
        frappe.throw(_("Only Librarians can perform this action"))
    
    # Proceed with operation
    doc = frappe.get_doc("Article", doc_name)
    doc.save()
```

### 2. Transactions

```python
def complex_operation():
    try:
        # Multiple database operations
        doc1.save()
        doc2.save()
        doc3.delete()
        
        frappe.db.commit()
    except Exception as e:
        frappe.db.rollback()
        frappe.log_error()
        raise
```

### 3. Background Jobs

```python
# Enqueue a background job
frappe.enqueue(
    method='library_management.tasks.send_reminders',
    queue='short',  # short, default, long
    timeout=300,
    is_async=True,
    job_name='send_overdue_reminders',
    arguments={'date': frappe.utils.today()}
)

# In tasks.py
def send_reminders(date):
    """Background task"""
    overdue = frappe.get_all(
        "Library Transaction",
        filters={"due_date": ["<", date], "status": "Issued"}
    )
    
    for transaction in overdue:
        send_email_reminder(transaction)
```

### 4. Fixtures for Initial Data

```python
# hooks.py
fixtures = [
    {
        "dt": "Custom Field",
        "filters": [["module", "=", "Library Management"]]
    },
    {
        "dt": "Role",
        "filters": [["name", "in", ["Librarian", "Library Member"]]]
    },
    "Library Settings"  # Single DocType
]

# Export fixtures
bench --site mysite.local export-fixtures

# Import on new site (automatic during install)
```

### 5. Caching

```python
# Cache for 1 hour
@frappe.cache(ttl=3600)
def get_popular_books():
    return frappe.db.sql("""
        SELECT name, title
        FROM `tabArticle`
        ORDER BY views DESC
        LIMIT 10
    """, as_dict=True)

# Clear cache
frappe.cache().delete_value("get_popular_books")
```

### 6. Translations

```python
# Use _() for translatable strings
from frappe import _

message = _("Book issued successfully")
error = _("Cannot issue more than {0} books").format(max_books)

# Generate translation files
bench --site mysite.local export-translations library_management hi
# Creates library_management/translations/hi.csv

# Build translations
bench build --app library_management
```

### 7. Email Notifications

```python
# Send email
frappe.sendmail(
    recipients=['user@example.com'],
    subject=_('Book Overdue Notice'),
    message=_('Your book is overdue. Please return it.'),
    reference_doctype='Library Transaction',
    reference_name=transaction.name
)

# Email template (in templates/)
frappe.sendmail(
    recipients=['user@example.com'],
    template='overdue_notice',
    args={
        'member_name': member.name,
        'book_title': article.title
    },
    reference_doctype='Library Transaction',
    reference_name=transaction.name
)
```

---

## Troubleshooting Guide

### Common Issues & Solutions

**1. "DocType not found" after creation**
```bash
# Clear cache
bench --site mysite.local clear-cache
# Or from Desk: Settings → Reload
```

**2. "Cannot import module" errors**
```bash
# Ensure app is installed
bench --site mysite.local list-apps

# Reinstall if needed
bench --site mysite.local uninstall-app library_management
bench --site mysite.local install-app library_management
```

**3. "Permission denied" when creating DocTypes**
```bash
# Enable developer mode
bench --site mysite.local set-config developer_mode 1
bench restart
```

**4. JavaScript changes not reflecting**
```bash
# Build assets
bench build --app library_management

# Or enable watch mode for development
bench watch
```

**5. Database schema mismatch**
```bash
# Run migrations
bench --site mysite.local migrate
```

**6. Missing child DocType errors**
```python
# Ensure child DocType is created BEFORE parent
# In DocType JSON, check "istable": 1
# Install in correct order via fixtures or manual creation
```

**7. "Site does not exist"**
```bash
# Check sites/apps.txt
cat sites/apps.txt

# Ensure app is listed
# If not, manually add and run:
bench --site mysite.local install-app library_management
```

### Diagnostic Tools

**View Error Logs:**
```bash
# Site-specific errors
tail -f sites/mysite.local/logs/error.log

# General bench logs
tail -f logs/bench.log

# Background job logs
tail -f logs/worker.log
```

**Database Console:**
```bash
# Access MariaDB
bench --site mysite.local mariadb

# Useful queries
SHOW TABLES LIKE 'tab%';
DESCRIBE `tabArticle`;
SELECT * FROM `tabError Log` ORDER BY creation DESC LIMIT 10;
```

**Check Services:**
```bash
# Redis
redis-cli ping

# MariaDB
systemctl status mariadb

# Check ports
sudo netstat -tlnp | grep -E '8000|9000|6379|3306'
```

---

## Advanced Topics (Brief Overview)

### Custom Pages & Reports

Create custom dashboard pages and reports using Frappe's Page API and Report Builder.

### Portal Development

Build customer-facing portals using Frappe's website module.

### Workflow & State Machines

Implement complex approval workflows using DocType workflows.

### Multi-tenant Architecture

Frappe is designed for multi-tenancy - each site is a separate tenant with isolated data.

### Integration Patterns

- **Webhooks**: React to events in real-time
- **REST API**: Standard integration for external systems
- **RPC/XMLRPC**: Legacy integration support
- **OAuth**: Secure third-party authentication

### Performance Optimization

- Database indexing
- Query optimization
- Caching strategies
- Background job optimization
- CDN for static assets

---

## Learning Resources

### Official Documentation
- Frappe Framework: https://docs.frappe.io/framework
- API Reference: https://docs.frappe.io/framework/user/en/api
- Frappe Forum: https://discuss.frappe.io

### Community Resources
- Frappe School: https://frappe.school
- YouTube Channel: Frappe Framework
- GitHub: https://github.com/frappe/frappe

### Example Apps
- ERPNext: https://github.com/frappe/erpnext
- Frappe CRM: https://github.com/frappe/crm
- Library Management (tutorial): Part of framework docs

---

## Conclusion

Building custom Frappe apps requires understanding the framework's conventions and architecture. This guide covered:

✅ Environment setup and app creation  
✅ DocType modeling and controllers  
✅ Frontend development with client scripts  
✅ API development and integration  
✅ Testing and debugging techniques  
✅ Production deployment on all platforms (Frappe Cloud, AWS, local)  
✅ Common patterns and troubleshooting  

**Next Steps:**
1. Choose your deployment platform (Frappe Cloud for easiest start)
2. Setup development environment
3. Build a simple app following this guide
4. Study existing apps like ERPNext/FCRM for advanced patterns
5. Join the Frappe community forums
6. Contribute to open-source Frappe projects

**Key Principles:**
- **Developer Mode is Essential**: Always enable for custom app development
- **Follow Conventions**: Frappe's conventions enable rapid development
- **Use the ORM**: Leverage Frappe's DocType system instead of raw SQL
- **Test Thoroughly**: Write unit tests for critical business logic
- **Monitor Logs**: Check error logs frequently during development
- **Read Official Docs**: Always refer to official documentation for latest features
- **Choose Right Platform**: Frappe Cloud for ease, self-hosted for control

**Platform Selection Guide:**
- **Just Starting?** → Frappe Cloud (focus on app, not infrastructure)
- **Enterprise Deployment?** → Self-hosted on AWS/Azure
- **High Compliance Needs?** → On-premise servers
- **SaaS Product?** → Frappe Cloud (multi-tenancy built-in)
- **Learning/Testing?** → Local development environment

---

## License

This skill document is released under MIT License. Feel free to use, modify, and distribute.

## Contributing

This is an open-source resource. Contributions, corrections, and improvements are welcome!

**Repository:** https://github.com/[your-username]/frappe-custom-app-skill  
**Issues/PRs:** Welcome and encouraged!

---

## About This Skill

**Purpose:** Comprehensive guide for Frappe Framework custom app development  
**Intended For:** Developers at all levels building on Frappe  
**Compatible With:** Claude.ai, Claude Code, and human developers  
**Last Updated:** January 2025  
**Version:** 1.0  
**Frappe Version Covered:** v14, v15 (principles apply to all versions)

---

*Happy Frappe Development! 🚀*
