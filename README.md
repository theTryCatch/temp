/* your-component.styles.css */
.mat-table {
  overflow: hidden;
}

.mat-header-row,
.mat-row {
  /* Set a fixed height for rows */
  min-height: 48px;
}

.mat-row {
  /* Enable scrolling for the body */
  display: block;
  overflow-y: auto;
  max-height: calc(100vh - 150px); /* Adjust the max height as needed */
}

.mat-header-row {
  /* Force the header to remain visible */
  position: sticky;
  top: 0;
  background-color: #fff; /* Set background color as needed */
  z-index: 2; /* Ensure it's above the scrolling body */
}
