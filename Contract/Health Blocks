// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

/**
 * @title Health Blocks: Patient Data Integrity System
 * @notice A blockchain-based system to store and manage patient health records securely.
 * @dev Patients own their data and can grant or revoke access to healthcare providers.
 */

contract HealthBlocks {
    // -------------------------
    // STRUCTS AND VARIABLES
    // -------------------------

    struct PatientRecord {
        string name;
        string dataHash; // Hash of encrypted off-chain medical data (e.g., stored on IPFS)
        uint256 timestamp;
    }

    mapping(address => PatientRecord) private patientRecords;
    mapping(address => mapping(address => bool)) private accessPermissions; 
    // patient => (provider => accessGranted)

    // -------------------------
    // EVENTS
    // -------------------------
    event RecordAdded(address indexed patient, string dataHash, uint256 timestamp);
    event AccessGranted(address indexed patient, address indexed provider);
    event AccessRevoked(address indexed patient, address indexed provider);

    // -------------------------
    // CORE FUNCTIONS
    // -------------------------

    /**
     * @notice Adds or updates the patient’s health record hash.
     * @param _name Patient’s name.
     * @param _dataHash Hash of the encrypted patient record stored off-chain.
     */
    function addOrUpdateRecord(string calldata _name, string calldata _dataHash) external {
        patientRecords[msg.sender] = PatientRecord({
            name: _name,
            dataHash: _dataHash,
            timestamp: block.timestamp
        });

        emit RecordAdded(msg.sender, _dataHash, block.timestamp);
    }

    /**
     * @notice Grants access to a healthcare provider.
     * @param _provider Address of the provider.
     */
    function grantAccess(address _provider) external {
        accessPermissions[msg.sender][_provider] = true;
        emit AccessGranted(msg.sender, _provider);
    }

    /**
     * @notice Revokes access from a healthcare provider.
     * @param _provider Address of the provider.
     */
    function revokeAccess(address _provider) external {
        accessPermissions[msg.sender][_provider] = false;
        emit AccessRevoked(msg.sender, _provider);
    }

    /**
     * @notice Allows authorized providers to view a patient’s record.
     * @param _patient Address of the patient.
     * @return name Patient’s name.
     * @return dataHash Hash of the patient’s record.
     * @return timestamp Last update time.
     */
    function viewRecord(address _patient)
        external
        view
        returns (string memory name, string memory dataHash, uint256 timestamp)
    {
        require(
            _patient == msg.sender || accessPermissions[_patient][msg.sender],
            "Access denied: unauthorized viewer"
        );

        PatientRecord memory record = patientRecords[_patient];
        return (record.name, record.dataHash, record.timestamp);
    }
}
