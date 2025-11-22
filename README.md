bandobast-management/
│
├── public/
│   ├── index.html
│   └── favicon.ico
│
├── src/
│   ├── api/
│   │   └── axiosInstance.js          // axios base configuration
│   │
│   ├── assets/
│   │   ├── logo.png
│   │   ├── bg-pattern.svg
│   │   ├── sample1.jpg               // (your uploaded image)
│   │   └── sample2.jpg
│   │
│   ├── components/
│   │   ├── sidebar/
│   │   │   └── Sidebar.jsx           // Order Mgmt Sidebar with nested menus
│   │   ├── header/
│   │   │   └── Header.jsx            // AppBar / Navbar
│   │   └── common/
│   │       ├── StatusChip.jsx        // Active/Completed chip
│   │       ├── ProgressBar.jsx       // Resource allocation linear bar
│   │       └── ConfirmationDialog.jsx
│   │
│   ├── features/
│   │   ├── orders/
│   │   │   ├── OrderSlice.js         // Redux Toolkit slice for orders
│   │   │   ├── CreateOrder.jsx       // Create Bandobast order form page
│   │   │   ├── OrdersList.jsx        // View Orders page with filters + pagination
│   │   │   ├── ViewOrder.jsx         // Detailed order view page
│   │   │   └── EditOrder.jsx         // Edit Bandobast order
│   │   │
│   │   └── inbox/
│   │       └── Inbox.jsx             // Inbox for approvals
│   │
│   ├── layouts/
│   │   └── MainLayout.jsx            // Contains Sidebar + Header + Outlet
│   │
│   ├── routes/
│   │   └── AppRoutes.jsx             // All frontend routes
│   │
│   ├── store/
│   │   └── store.js                  // Redux Toolkit store configuration
│   │
│   ├── styles/
│   │   └── global.css                // Optional global css
│   │
│   ├── App.jsx                       // Root App (wraps layout + routes)
│   └── main.jsx                      // ReactDOM render (Vite)
│
├── package.json
├── vite.config.js
└── README.md
https://chatgpt.com/canvas/shared/69211644d4148191a6d8aabb5101258b
/*
Bandobast UI - React 19 + MUI version
- Uses hooks for state
- React Router v6 for routing
- axios for API calls (JSON)
- Redux Toolkit for global state (orders)
- MUI (Material UI) for UI components and icons
- Manpower allocation: includes Assigned To dropdown with roles: Addl. CP, DCP, ACP, PI, Men PC, Women PC
- Admin dropdown and blue highlight for active menu

How to use:
1) Create project (Vite recommended) and install deps:
   npm i react react-dom react-router-dom axios @reduxjs/toolkit react-redux @mui/material @emotion/react @emotion/styled @mui/icons-material
2) Save this file as **src/App.jsx** (preferred) or **src/App.js** — the code is valid JSX and will work with either extension in a React project.
3) This sample references your uploaded images via local paths; your environment will transform them to accessible URLs:
   - /mnt/data/WhatsApp Image 2025-11-20 at 21.05.27_c7d2973e.jpg
   - /mnt/data/WhatsApp Image 2025-11-20 at 21.06.56_ab600bf4.jpg
   - /mnt/data/d.jpg
*/

import React, { useState, useEffect } from 'react';
import { createRoot } from 'react-dom/client';
import { BrowserRouter, Routes, Route, Link, useNavigate, useLocation } from 'react-router-dom';
import axios from 'axios';
import { configureStore, createSlice } from '@reduxjs/toolkit';
import { Provider, useDispatch, useSelector } from 'react-redux';

// MUI
import Box from '@mui/material/Box';
import Drawer from '@mui/material/Drawer';
import List from '@mui/material/List';
import ListItem from '@mui/material/ListItem';
import ListItemButton from '@mui/material/ListItemButton';
import ListItemIcon from '@mui/material/ListItemIcon';
import ListItemText from '@mui/material/ListItemText';
import Toolbar from '@mui/material/Toolbar';
import AppBar from '@mui/material/AppBar';
import Typography from '@mui/material/Typography';
import Container from '@mui/material/Container';
import Button from '@mui/material/Button';
import TextField from '@mui/material/TextField';
import Select from '@mui/material/Select';
import MenuItem from '@mui/material/MenuItem';
import FormControl from '@mui/material/FormControl';
import InputLabel from '@mui/material/InputLabel';
import Paper from '@mui/material/Paper';
import Grid from '@mui/material/Grid';
import IconButton from '@mui/material/IconButton';
import Table from '@mui/material/Table';
import TableBody from '@mui/material/TableBody';
import TableCell from '@mui/material/TableCell';
import TableHead from '@mui/material/TableHead';
import TableRow from '@mui/material/TableRow';
import Card from '@mui/material/Card';
import CardMedia from '@mui/material/CardMedia';
import Stack from '@mui/material/Stack';
import Avatar from '@mui/material/Avatar';
import Divider from '@mui/material/Divider';

// Icons
import AddIcon from '@mui/icons-material/Add';
import InboxIcon from '@mui/icons-material/Inbox';
import ListIcon from '@mui/icons-material/List';
import CreateIcon from '@mui/icons-material/Create';
import DeleteIcon from '@mui/icons-material/Delete';
import ImageIcon from '@mui/icons-material/Image';
import PeopleIcon from '@mui/icons-material/People';
import VisibilityIcon from '@mui/icons-material/Visibility';
import EditIcon from '@mui/icons-material/Edit';

// MUI extra
import LinearProgress from '@mui/material/LinearProgress';
import Chip from '@mui/material/Chip';
import Pagination from '@mui/material/Pagination';

// ---------- Redux slice ----------
const ordersSlice = createSlice({
  name: 'orders',
  initialState: {
    list: [
      {
        id: 1,
        name: 'Republic Day Bandobast',
        purpose: 'Parade security',
        description: 'Provide security and crowd control along main route.',
        startDate: '2025-01-25',
        endDate: '2025-01-26',
        placeOfReporting: 'City Center Parade Ground',
        manpower: [
          { assignedTo: 'Addl. CP', count: 2 },
          { assignedTo: 'DCP', count: 4 },
        ],
        admin: 'Admin A',
        images: [
          '/mnt/data/WhatsApp Image 2025-11-20 at 21.05.27_c7d2973e.jpg',
          '/mnt/data/WhatsApp Image 2025-11-20 at 21.06.56_ab600bf4.jpg',
          '/mnt/data/d.jpg',
        ],
      },
    ],
  },
  reducers: {
    addOrder(state, action) {
      state.list.push({ id: Date.now(), ...action.payload });
    },
    removeOrder(state, action) {
      state.list = state.list.filter(o => o.id !== action.payload);
    },
  },
});

const { addOrder, removeOrder } = ordersSlice.actions;
const store = configureStore({ reducer: { orders: ordersSlice.reducer } });

// ---------- Constants ----------
const drawerWidth = 260;
const manpowerRoles = ['Addl. CP', 'DCP', 'ACP', 'PI', 'Men PC', 'Women PC'];
const adminOptions = ['Admin A', 'Admin B', 'Admin C'];

// ---------- Components ----------
function AppSidebar() {
  const location = useLocation();

  return (
    <Drawer
      variant="permanent"
      sx={{
        width: drawerWidth,
        flexShrink: 0,
        [`& .MuiDrawer-paper`]: { width: drawerWidth, boxSizing: 'border-box' },
      }}
    >
      <Toolbar sx={{ px: 2 }}>
        <Stack direction="row" alignItems="center" spacing={2}>
          <Avatar sx={{ bgcolor: '#1976d2' }}>B</Avatar>
          <Typography variant="h6">Bandobast</Typography>
        </Stack>
      </Toolbar>
      <Divider />
      <List>
        <ListItem>
          <ListItemIcon>
            <PeopleIcon sx={{ color: '#1976d2' }} />
          </ListItemIcon>
          <ListItemText primary="Order Management" primaryTypographyProps={{ fontWeight: 'bold' }} />
        </ListItem>

        <List component="div" disablePadding sx={{ pl: 4 }}>
          <ListItem disablePadding>
            <ListItemButton component={Link} to="/create" selected={location.pathname === '/create' || location.pathname === '/'} sx={{ '&.Mui-selected': { bgcolor: '#e3f2fd' } }}>
              <ListItemIcon>
                <CreateIcon sx={{ color: '#1976d2' }} />
              </ListItemIcon>
              <ListItemText primary="Create Bandobast Order" />
            </ListItemButton>
          </ListItem>

          <ListItem disablePadding>
            <ListItemButton component={Link} to="/orders" selected={location.pathname === '/orders'} sx={{ '&.Mui-selected': { bgcolor: '#e3f2fd' } }}>
              <ListItemIcon>
                <ListIcon sx={{ color: '#1976d2' }} />
              </ListItemIcon>
              <ListItemText primary="View Orders" />
            </ListItemButton>
          </ListItem>
        </List>

        <ListItem disablePadding>
          <ListItemButton component={Link} to="/inbox" selected={location.pathname === '/inbox'} sx={{ '&.Mui-selected': { bgcolor: '#e3f2fd' } }}>
            <ListItemIcon>
              <InboxIcon sx={{ color: '#1976d2' }} />
            </ListItemIcon>
            <ListItemText primary="Bandobast Inbox" />
          </ListItemButton>
        </ListItem>
      </List>
    </Drawer>
  );
}

function OrdersList() {
  const orders = useSelector(s => s.orders.list);
  const dispatch = useDispatch();
  const navigate = useNavigate();

  const [filterStart, setFilterStart] = useState('');
  const [filterEnd, setFilterEnd] = useState('');
  const [page, setPage] = useState(1);
  const [rowsPerPage, setRowsPerPage] = useState(5);

  // compute filtered orders based on date filters
  const filtered = orders.filter(o => {
    if (filterStart && filterEnd) {
      return o.startDate >= filterStart && o.endDate <= filterEnd;
    }
    if (filterStart) return o.startDate >= filterStart;
    if (filterEnd) return o.endDate <= filterEnd;
    return true;
  });

  // pagination
  const totalPages = Math.max(1, Math.ceil(filtered.length / rowsPerPage));
  useEffect(() => {
    // if current page exceeds total pages after filter change, reset to 1
    if (page > totalPages) setPage(1);
  }, [filtered.length, rowsPerPage, totalPages]);

  function clearFilters() {
    setFilterStart('');
    setFilterEnd('');
    setPage(1);
  }

  const handlePageChange = (event, value) => setPage(value);

  // helper to compute resource allocation percent (simple heuristic)
  const computeAllocation = (order) => {
    const total = order.manpower ? order.manpower.reduce((s, r) => s + (r.count || 0), 0) : 0;
    const percent = total ? Math.min(100, Math.round((total / 200) * 100)) : 0;
    return percent;
  }

  // slice for current page
  const startIdx = (page - 1) * rowsPerPage;
  const paginated = filtered.slice(startIdx, startIdx + rowsPerPage);

  return (
    <Box>
      <Box mb={3}>
        <Typography variant="h5">All Bandobast Orders</Typography>
        <Typography variant="body2" color="text.secondary" sx={{ mt: 1 }}>Overview of all created bandobast security order</Typography>

        <Grid container spacing={2} alignItems="center" sx={{ mt: 2 }}>
          <Grid item xs={12} md={6} />

          <Grid item xs={12} md={6}>
            <Box display="flex" gap={2} justifyContent="flex-end" alignItems="center">
              <TextField label="Start Date" type="date" size="small" InputLabelProps={{ shrink: true }} value={filterStart} onChange={e => { setFilterStart(e.target.value); setPage(1); }} />
              <TextField label="End Date" type="date" size="small" InputLabelProps={{ shrink: true }} value={filterEnd} onChange={e => { setFilterEnd(e.target.value); setPage(1); }} />

              <Button variant="outlined" onClick={clearFilters}>View All Orders</Button>
            </Box>
          </Grid>
        </Grid>
      </Box>

      {/* Header row */}
      <Paper sx={{ p: 1, mb: 1 }} elevation={0}>
        <Grid container spacing={2} alignItems="center">
          <Grid item xs={12} md={3}><Typography variant="subtitle2" sx={{ fontWeight: 700 }}>Order name</Typography></Grid>
          <Grid item xs={6} md={2}><Typography variant="subtitle2" sx={{ fontWeight: 700 }}>Date range</Typography></Grid>
          <Grid item xs={6} md={2}><Typography variant="subtitle2" sx={{ fontWeight: 700 }}>Location</Typography></Grid>
          <Grid item xs={6} md={1}><Typography variant="subtitle2" sx={{ fontWeight: 700 }}>Status</Typography></Grid>
          <Grid item xs={6} md={3}><Typography variant="subtitle2" sx={{ fontWeight: 700 }}>Resource Allocation</Typography></Grid>
          <Grid item xs={12} md={1} sx={{ textAlign: 'center' }}><Typography variant="subtitle2" sx={{ fontWeight: 700 }}>Action</Typography></Grid>
        </Grid>
      </Paper>

      {/* Rows: each row's fields placed under matching header columns */}
      <Box>
        {paginated.map(o => {
          const percent = computeAllocation(o);
          const today = new Date().toISOString().slice(0,10);
          const status = o.endDate < today ? 'Completed' : 'Active';

          return (
            <Paper key={o.id} sx={{ p:1, mb:1 }}>
              <Grid container spacing={2} alignItems="center">
                <Grid item xs={12} md={3}>
                  <Stack direction="row" alignItems="center" spacing={2}>
                    <Box sx={{ width: 36, height:36, bgcolor: '#e3f2fd', display: 'flex', alignItems: 'center', justifyContent: 'center', borderRadius: 1 }}>
                      <Avatar sx={{ bgcolor: '#1976d2', width: 28, height: 28 }}>{o.name ? o.name.charAt(0) : 'B'}</Avatar>
                    </Box>
                    <Box>
                      <Typography variant="subtitle1" sx={{ fontWeight: 600 }}>{o.name}</Typography>
                      <Typography variant="body2" color="text.secondary">{o.description}</Typography>
                    </Box>
                  </Stack>
                </Grid>

                <Grid item xs={6} md={2}>
                  <Typography variant="body2">{o.startDate} → {o.endDate}</Typography>
                </Grid>

                <Grid item xs={6} md={2}>
                  <Typography variant="body2">{o.placeOfReporting}</Typography>
                </Grid>

                <Grid item xs={6} md={1}>
                  <Chip label={status} color={status === 'Active' ? 'success' : 'default'} size="small" />
                </Grid>

                <Grid item xs={6} md={3}>
                  <Typography variant="caption">{percent}%</Typography>
                  <LinearProgress variant="determinate" value={percent} sx={{ height: 10, borderRadius: 2, mt: 0.5 }} />
                </Grid>

                <Grid item xs={12} md={1} sx={{ textAlign: 'center' }}>
                  <Stack direction="row" spacing={1} justifyContent="center">
                    <IconButton size="small" onClick={() => navigate(`/orders/view/${o.id}`)} title="View"><VisibilityIcon /></IconButton>
                    <IconButton size="small" onClick={() => navigate(`/orders/edit/${o.id}`)} title="Edit"><EditIcon /></IconButton>
                    <IconButton size="small" color="error" onClick={() => dispatch(removeOrder(o.id))} title="Delete"><DeleteIcon /></IconButton>
                  </Stack>
                </Grid>
              </Grid>
            </Paper>
          )
        })}
      </Box>

      {/* Pagination controls */}
      <Box sx={{ display: 'flex', justifyContent: 'center', mt: 2 }}>
        <Pagination count={totalPages} page={page} onChange={handlePageChange} color="primary" />
      </Box>
    </Box>
  );
}

function BandobastInbox() {
  return (
    <Box>
      <Typography variant="h5" gutterBottom>Bandobast Inbox</Typography>
      <Typography color="text.secondary">Placeholder for approvals, messages and workflow actions.</Typography>
    </Box>
  );
}

function CreateBandobast() {
  const dispatch = useDispatch();
  const navigate = useNavigate();

  const [form, setForm] = useState({
    name: '',
    purpose: '',
    description: '',
    startDate: '',
    endDate: '',
    placeOfReporting: '',
    // manpower now organized per-department with role counts
    departments: [
      { name: 'South Region Office', roles: { 'Addl. CP': 0, 'DCP': 0, 'ACP': 0, 'PC Men': 0, 'PC Women': 0 } },
      { name: 'North Region Office', roles: { 'Addl. CP': 0, 'DCP': 0, 'ACP': 0, 'PC Men': 0, 'PC Women': 0 } },
      { name: 'Marine Drive Police Station', roles: { 'Addl. CP': 0, 'DCP': 0, 'ACP': 0, 'PC Men': 0, 'PC Women': 0 } },
    ],
    admin: '',
  });

  function handleChange(e) {
    const { name, value } = e.target;
    setForm(prev => ({ ...prev, [name]: value }));
  }

  function updateDepartmentName(idx, val) {
    setForm(prev => {
      const deps = [...prev.departments];
      deps[idx] = { ...deps[idx], name: val };
      return { ...prev, departments: deps };
    });
  }

  function updateRoleCount(depIdx, role, count) {
    setForm(prev => {
      const deps = [...prev.departments];
      const roles = { ...deps[depIdx].roles, [role]: Number(count) };
      deps[depIdx] = { ...deps[depIdx], roles };
      return { ...prev, departments: deps };
    });
  }

  function addDepartment() {
    setForm(prev => ({ ...prev, departments: [...prev.departments, { name: 'New Department', roles: { 'Addl. CP': 0, 'DCP': 0, 'ACP': 0, 'PC Men': 0, 'PC Women': 0 } }] }));
  }

  function removeDepartment(idx) {
    setForm(prev => ({ ...prev, departments: prev.departments.filter((_, i) => i !== idx) }));
  }

  async function handleSubmit(e) {
    e.preventDefault();
    const payload = { ...form };
    dispatch(addOrder(payload));

    try {
      await axios.post('/api/bandobast', payload, { headers: { 'Content-Type': 'application/json' } });
      navigate('/orders');
    } catch (err) {
      console.warn('POST failed (demo):', err.message);
      navigate('/orders');
    }
  }

  return (
    <Box>
      <Typography variant="h4" gutterBottom>Create Bandobast Order</Typography>
      <Typography variant="h6" gutterBottom sx={{ mt: 1 }}>Bandobast Details</Typography>

      <Paper sx={{ p: 3 }}>
        <Grid container spacing={2} component="form" onSubmit={handleSubmit}>
          {/* Enter basic information field under the heading */}
          <Grid item xs={12}>
            <TextField label="Enter basic information for Bandobast order" name="title" fullWidth placeholder="e.g. Republic Day details" onChange={(e)=>{ handleChange({ target: { name: 'name', value: e.target.value }})}} />
          </Grid>

          {/* Bandobast Name and Purpose side-by-side behind the basic info textfield */}
          <Grid item xs={12} md={6}>
            <TextField label="Bandobast Name" name="name" fullWidth value={form.name} onChange={handleChange} />
          </Grid>

          <Grid item xs={12} md={6}>
            <FormControl fullWidth>
              <InputLabel>Purpose</InputLabel>
              <Select name="purpose" value={form.purpose} label="Purpose" onChange={handleChange}>
                <MenuItem value="">Select Purpose</MenuItem>
                <MenuItem value="Parade security">Parade security</MenuItem>
                <MenuItem value="VIP protection">VIP protection</MenuItem>
                <MenuItem value="Crowd control">Crowd control</MenuItem>
                <MenuItem value="Traffic management">Traffic management</MenuItem>
              </Select>
            </FormControl>
          </Grid>

          {/* Description comes after name & purpose */}
          <Grid item xs={12}>
            <TextField label="Description" name="description" fullWidth multiline rows={3} value={form.description} onChange={handleChange} placeholder="Provide basic description" />
          </Grid>

          {/* Start Date and End Date after description */}
          <Grid item xs={6} md={3}>
            <TextField label="Start Date" name="startDate" type="date" fullWidth InputLabelProps={{ shrink: true }} value={form.startDate} onChange={handleChange} />
          </Grid>

          <Grid item xs={6} md={3}>
            <TextField label="End Date" name="endDate" type="date" fullWidth InputLabelProps={{ shrink: true }} value={form.endDate} onChange={handleChange} />
          </Grid>

          <Grid item xs={12}>
            <TextField label="Place of Reporting" name="placeOfReporting" fullWidth value={form.placeOfReporting} onChange={handleChange} />
          </Grid>

          {/* Manpower allocation section matching the sketch: department rows with role columns */}
          <Grid item xs={12}>
            <Typography variant="h6" sx={{ mt: 3 }}>Manpower Allocation</Typography>
            <Typography variant="body2" color="text.secondary">Specify the number of personnel required from each department and rank</Typography>

            <Table size="small" sx={{ mt: 2 }}>
              <TableHead>
                <TableRow>
                  <TableCell>Assigned To / Department</TableCell>
                  <TableCell>Addl. CP</TableCell>
                  <TableCell>DCP</TableCell>
                  <TableCell>ACP</TableCell>
                  <TableCell>PC Men</TableCell>
                  <TableCell>PC Women</TableCell>
                  <TableCell>Admin</TableCell>
                </TableRow>
              </TableHead>
              <TableBody>
                {form.departments.map((dep, idx) => (
                  <TableRow key={idx}>
                    <TableCell sx={{ minWidth: 200 }}>
                      <TextField value={dep.name} onChange={e => updateDepartmentName(idx, e.target.value)} fullWidth size="small" />
                    </TableCell>

                    <TableCell>
                      <TextField type="number" size="small" value={dep.roles['Addl. CP']} onChange={e => updateRoleCount(idx, 'Addl. CP', e.target.value)} sx={{ width: 80 }} />
                    </TableCell>

                    <TableCell>
                      <TextField type="number" size="small" value={dep.roles['DCP']} onChange={e => updateRoleCount(idx, 'DCP', e.target.value)} sx={{ width: 80 }} />
                    </TableCell>

                    <TableCell>
                      <TextField type="number" size="small" value={dep.roles['ACP']} onChange={e => updateRoleCount(idx, 'ACP', e.target.value)} sx={{ width: 80 }} />
                    </TableCell>

                    <TableCell>
                      <TextField type="number" size="small" value={dep.roles['PC Men']} onChange={e => updateRoleCount(idx, 'PC Men', e.target.value)} sx={{ width: 80 }} />
                    </TableCell>

                    <TableCell>
                      <TextField type="number" size="small" value={dep.roles['PC Women']} onChange={e => updateRoleCount(idx, 'PC Women', e.target.value)} sx={{ width: 80 }} />
                    </TableCell>

                    <TableCell>
                      <FormControl fullWidth size="small">
                        <Select value={form.admin} onChange={handleChange} name="admin">
                          <MenuItem value="">Select Admin</MenuItem>
                          {adminOptions.map(a => <MenuItem key={a} value={a}>{a}</MenuItem>)}
                        </Select>
                      </FormControl>
                    </TableCell>
                  </TableRow>
                ))}
              </TableBody>
            </Table>

            <Button startIcon={<AddIcon />} sx={{ mt: 2 }} onClick={addDepartment}>+ Add Department</Button>
          </Grid>

          <Grid item xs={12} sx={{ display: 'flex', gap: 1, justifyContent: 'flex-end' }}>
            <Button variant="outlined" onClick={() => navigate('/orders')}>Cancel</Button>
            <Button type="submit" variant="contained" color="primary">Create Bandobast Order</Button>
          </Grid>
        </Grid>
      </Paper>
    </Box>
  );
}

function AppShell() {
  return (
    <Box sx={{ display: 'flex' }}>
      <AppBar position="fixed" sx={{ zIndex: theme => theme.zIndex.drawer + 1 }}>
        <Toolbar>
          <Typography variant="h6" noWrap component="div">Bandobast Management</Typography>
        </Toolbar>
      </AppBar>

      <AppSidebar />

      <Box component="main" sx={{ flexGrow: 1, p: 3 }}>
        <Toolbar />
        <Routes>
          <Route path="/" element={<CreateBandobast />} />
          <Route path="/create" element={<CreateBandobast />} />
          <Route path="/orders" element={<OrdersList />} />
          <Route path="/inbox" element={<BandobastInbox />} />
        </Routes>
      </Box>
    </Box>
  );
}

// ---------- Render ----------
function Root() {
  return (
    <Provider store={store}>
      <BrowserRouter>
        <AppShell />
      </BrowserRouter>
    </Provider>
  );
}

const container = document.getElementById('root');
if (container) createRoot(container).render(<Root />);

export default Root;
















<img width="1542" height="834" alt="image" src="https://github.com/user-attachments/assets/b9fbd468-7979-442b-b43b-9760f478cfec" />


<img width="1541" height="839" alt="image" src="https://github.com/user-attachments/assets/2ba409a3-4c71-48ea-ba7a-7f791227e944" />

<img width="1542" height="834" alt="image" src="https://github.com/user-attachments/assets/53efdcaa-7155-46cb-9658-d58960d4ba89" />

