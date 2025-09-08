import React, { useState, useEffect } from "react";
import axios from "axios";

export default function UserForm() {
  const [formData, setFormData] = useState({
    name: "",
    email: "",
    password: "",
    role: "",
    folderId: ""
  });

  const [folders, setFolders] = useState([]);

  // Fetch folders when role changes
  useEffect(() => {
    if (formData.role === "user") {
      axios.get("/api/user/subfolders")
        .then(res => setFolders(res.data))
        .catch(err => console.error("Error fetching user subfolders:", err));
    } else if (formData.role === "ops") {
      axios.get("/api/ops/subfolders")
        .then(res => setFolders(res.data))
        .catch(err => console.error("Error fetching ops subfolders:", err));
    } else {
      setFolders([]); // reset if no role selected
    }
  }, [formData.role]);

  const handleChange = (e) => {
    setFormData({ ...formData, [e.target.name]: e.target.value });
  };

  const handleSubmit = (e) => {
    e.preventDefault();

    axios.post("/api/users", formData)
      .then(res => {
        alert("User saved successfully!");
        console.log(res.data);
      })
      .catch(err => {
        alert("Error saving user!");
        console.error(err);
      });
  };

  return (
    <div className="max-w-md mx-auto p-6 bg-white rounded-xl shadow-md">
      <h2 className="text-xl font-bold mb-4">Add User</h2>
      <form onSubmit={handleSubmit} className="space-y-4">

        {/* Name */}
        <input
          type="text"
          name="name"
          placeholder="Name"
          value={formData.name}
          onChange={handleChange}
          className="w-full border p-2 rounded"
          required
        />

        {/* Email */}
        <input
          type="email"
          name="email"
          placeholder="Email"
          value={formData.email}
          onChange={handleChange}
          className="w-full border p-2 rounded"
          required
        />

        {/* Password */}
        <input
          type="password"
          name="password"
          placeholder="Password"
          value={formData.password}
          onChange={handleChange}
          className="w-full border p-2 rounded"
          required
        />

        {/* Role Dropdown */}
        <select
          name="role"
          value={formData.role}
          onChange={handleChange}
          className="w-full border p-2 rounded"
          required
        >
          <option value="">Select Role</option>
          <option value="user">User</option>
          <option value="ops">Ops</option>
        </select>

        {/* Folder Dropdown */}
        <select
          name="folderId"
          value={formData.folderId}
          onChange={handleChange}
          className="w-full border p-2 rounded"
          required
          disabled={!folders.length}
        >
          <option value="">Select Folder</option>
          {folders.map(folder => (
            <option key={folder.id} value={folder.id}>
              {folder.name}
            </option>
          ))}
        </select>

        {/* Save Button */}
        <button
          type="submit"
          className="w-full bg-blue-600 text-white py-2 rounded hover:bg-blue-700"
        >
          Save
        </button>
      </form>
    </div>
  );
}
