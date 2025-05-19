# solidity-assignments-vigneshjraju
solidity-assignments-vigneshjraju created by GitHub Classroom

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract StudentManagementSystem {
    // Define student structure
    enum Department { CS, EC, ME }

    struct Student {
        string name;
        uint8 sem;
        Department dept;
        uint256 cgpa;
        uint256 rollNo;
        address studentAddress;
    }

    address public staffAdvisor;
    Student[] public students;

    // Map address to index in the array for easy update
    mapping(address => uint256) private studentIndex;
    mapping(address => bool) public isStudent;

    constructor() {
        staffAdvisor = msg.sender; // deployer is staff advisor
    }

    // Modifier: Only staff advisor can do this
    modifier onlyStaffAdvisor() {
        require(msg.sender == staffAdvisor, "Only staff advisor allowed");
        _;
    }

    // Modifier: Only student can do this
    modifier onlyStudent() {
        require(isStudent[msg.sender], "Only student allowed");
        _;
    }

    // Add a new student (only by staff advisor)
    function addStudent(
        string memory _name,
        uint8 _sem,
        Department _dept,
        uint256 _cgpa,
        uint256 _rollNo,
        address _studentAddress
    ) public onlyStaffAdvisor {
        Student memory s = Student(_name, _sem, _dept, _cgpa, _rollNo, _studentAddress);
        students.push(s);
        studentIndex[_studentAddress] = students.length - 1;
        isStudent[_studentAddress] = true;
    }

    // Staff advisor can edit full student details
    function editStudent(
        address _studentAddress,
        string memory _name,
        uint8 _sem,
        Department _dept,
        uint256 _cgpa,
        uint256 _rollNo
    ) public onlyStaffAdvisor {
        uint256 index = studentIndex[_studentAddress];
        students[index] = Student(_name, _sem, _dept, _cgpa, _rollNo, _studentAddress);
    }

    // Student can only update their name
    function updateMyName(string memory _newName) public onlyStudent {
        uint256 index = studentIndex[msg.sender];
        students[index].name = _newName;
    }

    // Staff advisor can view any student details by index
    function getStudent(uint256 _index) public view onlyStaffAdvisor returns (
        string memory name,
        uint8 sem,
        Department dept,
        uint256 cgpa,
        uint256 rollNo,
        address studentAddress
    ) {
        Student memory s = students[_index];
        return (s.name, s.sem, s.dept, s.cgpa, s.rollNo, s.studentAddress);
    }

    // Get number of students
    function getStudentCount() public view returns (uint256) {
        return students.length;
    }
}
