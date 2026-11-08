 AI-LABS

BS CS AI Practical Lab Tasks and Assignments

# AI Lab 1: Introduction to AI & Knowledge-Based Systems

---

## Exercise 1: Degree Honors Rule

### Question / Task Statement

> Add a new rule to 
> UNIVERSITY_KB["rules"] for students completing their degree program:
> 
> * *Condition:* cgpa >= 3.70 and disciplinary_warnings == 0
> * *Status:* "Graduated with Magna Cum Laude Honors"

### Solution

```python
UNIVERSITY_KB["rules"].append({
    "id": "RULE_07",
    "conditions": {"cgpa_min": 3.70, "warnings": 0},
    "condition_type": "honors_check",
    "grade": "A+",
    "status": "Graduated with Magna Cum Laude Honors",
    "action": "Eligible for degree conferral with high honors."
})
```
## Exercise 2: Tracing Forward Chaining

### Question / Task Statement

Modify `evaluate_student()` to print each evaluated rule condition as the loop runs, demonstrating step-by-step logical deduction.

### Solution

```python
def evaluate_student(student_record, kb):
    """
    Evaluates student marks and attendance against university rules.
    Prints each rule condition evaluated for step-by-step tracing.
    """
    print(f"\n--- EVALUATING STUDENT: {student_record['student_name']} (ID: {student_record['student_id']}) ---")
    
    # Rule 1: Attendance check
    print("Checking RULE_01: Attendance threshold...")
    if student_record["attendance"] < 75:
        print(f"Condition satisfied: Attendance is {student_record['attendance']}% (Below 75%)")
        attendance_rule = next(r for r in kb["rules"] if r["condition_type"] == "attendance_below")
        return {
            "rule_id": attendance_rule["id"],
            "grade": attendance_rule["grade"],
            "status": attendance_rule["status"],
            "action": attendance_rule["action"]
        }
    else:
        print(f"Condition failed: Attendance is {student_record['attendance']}% (Passed)")

    # Grade rules
    marks = student_record["total_marks"]
    print(f"Checking marks range for {marks} marks...")

    for rule in kb["rules"]:
        if rule["condition_type"] == "marks_range":
            min_marks = rule["conditions"]["marks_min"]
            max_marks = rule["conditions"]["marks_max"]
            
            print(f"Evaluating {rule['id']}: Is {marks} between {min_marks} and {max_marks}?")
            
            if min_marks <= marks <= max_marks:
                print(f"Rule matched: {rule['id']} - Grade {rule['grade']}")
                return {
                    "rule_id": rule["id"],
                    "grade": rule["grade"],
                    "status": rule["status"],
                    "action": rule["action"]
                }
                
    return {
        "rule_id": "UNKNOWN",
        "grade": "N/A",
        "status": "Invalid Score Entry",
        "action": "Verify submitted marks with the exam department."
    }

