import React, { useState, useEffect } from "react";
import axios from "axios";
import "./admin.css";

export function AddUserModal({ isOpen, onClose, onSave, defaultData }) {
  const [formData, setFormData] = useState({
    fullName: "",
    emailAddress: "",
    role: "user",
    branchAccess: "" // single value instead of array
  });

  const [availableFolders, setAvailableFolders] = useState([]);

  // Prefill form when editing
  useEffect(() => {
    if (defaultData) {
      setFormData({
        fullName: defaultData.name || "",
        emailAddress: defaultData.email || "",
        role: defaultData.role || "user",
        branchAccess: defaultData.branch || "" // single branch value
      });
    }
  }, [defaultData]);

  // Fetch folders whenever role changes
  useEffect(() => {
    if (formData.role) {
      axios
        .get(`/api/folders/${formData.role}`)
        .then((res) => {
          setAvailableFolders(res.data); // assume [{id, name}]
        })
        .catch((err) => {
          console.error("Error fetching folders", err);
          setAvailableFolders([]);
        });
    }
  }, [formData.role]);

  const handleSave = () => {
    onSave(formData);
    setFormData({
      fullName: "",
      emailAddress: "",
      role: "user",
      branchAccess: ""
    });
    onClose();
  };

  if (!isOpen) return null;

  return (
    <div className="modal-overlay">
      <div className="modal-content" style={{ width: "600px" }}>
        <div className="modal-header">
          <h5>{defaultData ? "Edit User" : "Add New User"}</h5>
          <button className="btn-close" onClick={onClose}></button>
        </div>

        <div className="modal-body" style={{ padding: "20px" }}>
          <div className="row">
            {/* Left side form fields */}
            <div className="col-md-6">
              <div className="form-group mb-3">
                <label className="form-label">Full Name</label>
                <input
                  type="text"
                  className="form-control"
                  value={formData.fullName}
                  onChange={(e) =>
                    setFormData((prev) => ({ ...prev, fullName: e.target.value }))
                  }
                />
              </div>

              <div className="form-group mb-3">
                <label className="form-label">Email Address</label>
                <input
                  type="email"
                  className="form-control"
                  value={formData.emailAddress}
                  onChange={(e) =>
                    setFormData((prev) => ({
                      ...prev,
                      emailAddress: e.target.value
                    }))
                  }
                />
              </div>

              <div className="form-group mb-3">
                <label className="form-label">Role</label>
                <select
                  className="form-select"
                  value={formData.role}
                  onChange={(e) =>
                    setFormData((prev) => ({ ...prev, role: e.target.value }))
                  }
                >
                  <option value="user">User</option>
                  <option value="ops">Ops</option>
                  <option value="admin">Admin</option>
                </select>
              </div>
            </div>

            {/* Right side: Single-select Dropdown for folder access */}
            <div className="col-md-6">
              <label className="form-label">Folder Access</label>
              <select
                className="form-select"
                value={formData.branchAccess}
                onChange={(e) =>
                  setFormData((prev) => ({
                    ...prev,
                    branchAccess: e.target.value
                  }))
                }
              >
                <option value="">-- Select Folder --</option>
                {availableFolders.map((folder) => (
                  <option key={folder.id} value={folder.name}>
                    {folder.name}
                  </option>
                ))}
              </select>
            </div>
          </div>
        </div>

        <div className="modal-footer">
          <button className="btn btn-secondary me-2" onClick={onClose}>
            Cancel
          </button>
          <button className="btn btn-primary" onClick={handleSave}>
            {defaultData ? "Update User" : "Create User"}
          </button>
        </div>
      </div>
    </div>
  );
}
