# Firebase Firestore Integration Guide for Ganpati Tracker

## Step 1: Create Firebase Project

1. **Go to Firebase Console**
   - Visit: https://console.firebase.google.com/
   - Sign in with your Google account

2. **Create New Project**
   - Click "Create a project"
   - Enter project name: `ganpati-tracker`
   - Enable Google Analytics (optional)
   - Click "Create project"

3. **Add Web App**
   - Click the web icon `</>`
   - Enter app nickname: `Ganpati Tracker Web`
   - Check "Also set up Firebase Hosting" (optional)
   - Click "Register app"

## Step 2: Set up Firestore Database

1. **Create Firestore Database**
   - In Firebase Console, go to "Firestore Database"
   - Click "Create database"
   - Choose "Start in test mode" (for development)
   - Select location closest to your users
   - Click "Done"

2. **Configure Security Rules** (Initial - for development)
   ```javascript
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /{document=**} {
         allow read, write: if request.time < timestamp.date(2024, 12, 31);
       }
     }
   }
   ```

## Step 3: Get Firebase Configuration

Copy the Firebase configuration object from the Firebase Console:

```javascript
// Your Firebase config (replace with your actual config)
const firebaseConfig = {
  apiKey: "your-api-key",
  authDomain: "ganpati-tracker.firebaseapp.com",
  projectId: "ganpati-tracker",
  storageBucket: "ganpati-tracker.appspot.com",
  messagingSenderId: "123456789",
  appId: "your-app-id"
};
```

## Step 4: Implementation Files

### 1. Create `js/firebase-config.js`

```javascript
// Firebase Configuration and Initialization
// Replace the config below with your actual Firebase config

const firebaseConfig = {
  apiKey: "your-api-key",
  authDomain: "ganpati-tracker.firebaseapp.com",
  projectId: "ganpati-tracker",
  storageBucket: "ganpati-tracker.appspot.com",
  messagingSenderId: "123456789",
  appId: "your-app-id"
};

// Initialize Firebase
import { initializeApp } from 'firebase/app';
import { getFirestore } from 'firebase/firestore';
import { getAuth } from 'firebase/auth';

const app = initializeApp(firebaseConfig);
export const db = getFirestore(app);
export const auth = getAuth(app);

console.log('Firebase initialized successfully');
```

### 2. Create `js/firestore-service.js`

```javascript
// Firestore Database Service
import { db } from './firebase-config.js';
import { 
  collection, 
  addDoc, 
  getDocs, 
  doc, 
  updateDoc, 
  deleteDoc, 
  onSnapshot,
  query,
  orderBy,
  where,
  serverTimestamp 
} from 'firebase/firestore';

class FirestoreService {
  constructor() {
    this.collections = {
      donations: 'donations',
      expenses: 'expenses',
      users: 'users',
      analytics: 'analytics'
    };
  }

  // DONATIONS CRUD OPERATIONS
  
  // Add new donation
  async addDonation(donationData) {
    try {
      const docRef = await addDoc(collection(db, this.collections.donations), {
        ...donationData,
        timestamp: serverTimestamp(),
        createdAt: new Date().toISOString()
      });
      console.log('Donation added with ID: ', docRef.id);
      return docRef.id;
    } catch (error) {
      console.error('Error adding donation: ', error);
      throw error;
    }
  }

  // Get all donations
  async getDonations() {
    try {
      const q = query(
        collection(db, this.collections.donations), 
        orderBy('timestamp', 'desc')
      );
      const querySnapshot = await getDocs(q);
      const donations = [];
      querySnapshot.forEach((doc) => {
        donations.push({ id: doc.id, ...doc.data() });
      });
      return donations;
    } catch (error) {
      console.error('Error getting donations: ', error);
      throw error;
    }
  }

  // Listen to donations in real-time
  onDonationsChange(callback) {
    const q = query(
      collection(db, this.collections.donations), 
      orderBy('timestamp', 'desc')
    );
    return onSnapshot(q, (querySnapshot) => {
      const donations = [];
      querySnapshot.forEach((doc) => {
        donations.push({ id: doc.id, ...doc.data() });
      });
      callback(donations);
    });
  }

  // Update donation
  async updateDonation(donationId, updateData) {
    try {
      const donationRef = doc(db, this.collections.donations, donationId);
      await updateDoc(donationRef, {
        ...updateData,
        updatedAt: new Date().toISOString()
      });
      console.log('Donation updated successfully');
    } catch (error) {
      console.error('Error updating donation: ', error);
      throw error;
    }
  }

  // Delete donation
  async deleteDonation(donationId) {
    try {
      await deleteDoc(doc(db, this.collections.donations, donationId));
      console.log('Donation deleted successfully');
    } catch (error) {
      console.error('Error deleting donation: ', error);
      throw error;
    }
  }

  // EXPENSES CRUD OPERATIONS

  // Add new expense
  async addExpense(expenseData) {
    try {
      const docRef = await addDoc(collection(db, this.collections.expenses), {
        ...expenseData,
        timestamp: serverTimestamp(),
        createdAt: new Date().toISOString()
      });
      console.log('Expense added with ID: ', docRef.id);
      return docRef.id;
    } catch (error) {
      console.error('Error adding expense: ', error);
      throw error;
    }
  }

  // Get all expenses
  async getExpenses() {
    try {
      const q = query(
        collection(db, this.collections.expenses), 
        orderBy('timestamp', 'desc')
      );
      const querySnapshot = await getDocs(q);
      const expenses = [];
      querySnapshot.forEach((doc) => {
        expenses.push({ id: doc.id, ...doc.data() });
      });
      return expenses;
    } catch (error) {
      console.error('Error getting expenses: ', error);
      throw error;
    }
  }

  // Listen to expenses in real-time
  onExpensesChange(callback) {
    const q = query(
      collection(db, this.collections.expenses), 
      orderBy('timestamp', 'desc')
    );
    return onSnapshot(q, (querySnapshot) => {
      const expenses = [];
      querySnapshot.forEach((doc) => {
        expenses.push({ id: doc.id, ...doc.data() });
      });
      callback(expenses);
    });
  }

  // Update expense
  async updateExpense(expenseId, updateData) {
    try {
      const expenseRef = doc(db, this.collections.expenses, expenseId);
      await updateDoc(expenseRef, {
        ...updateData,
        updatedAt: new Date().toISOString()
      });
      console.log('Expense updated successfully');
    } catch (error) {
      console.error('Error updating expense: ', error);
      throw error;
    }
  }

  // Delete expense
  async deleteExpense(expenseId) {
    try {
      await deleteDoc(doc(db, this.collections.expenses, expenseId));
      console.log('Expense deleted successfully');
    } catch (error) {
      console.error('Error deleting expense: ', error);
      throw error;
    }
  }

  // ANALYTICS OPERATIONS

  // Calculate and update analytics
  async updateAnalytics() {
    try {
      const donations = await this.getDonations();
      const expenses = await this.getExpenses();

      const totalDonations = donations.reduce((sum, donation) => 
        sum + parseFloat(donation.amount || 0), 0
      );
      
      const totalExpenses = expenses.reduce((sum, expense) => 
        sum + parseFloat(expense.amount || 0), 0
      );

      const balance = totalDonations - totalExpenses;
      const totalDonors = donations.length;

      const analyticsData = {
        totalDonations,
        totalExpenses,
        balance,
        totalDonors,
        lastUpdated: new Date().toISOString(),
        timestamp: serverTimestamp()
      };

      // Store analytics
      await addDoc(collection(db, this.collections.analytics), analyticsData);
      
      return analyticsData;
    } catch (error) {
      console.error('Error updating analytics: ', error);
      throw error;
    }
  }

  // Get latest analytics
  async getAnalytics() {
    try {
      const q = query(
        collection(db, this.collections.analytics), 
        orderBy('timestamp', 'desc')
      );
      const querySnapshot = await getDocs(q);
      
      if (!querySnapshot.empty) {
        const latestDoc = querySnapshot.docs[0];
        return { id: latestDoc.id, ...latestDoc.data() };
      }
      
      // If no analytics exist, calculate and return
      return await this.updateAnalytics();
    } catch (error) {
      console.error('Error getting analytics: ', error);
      throw error;
    }
  }

  // Listen to analytics changes
  onAnalyticsChange(callback) {
    const q = query(
      collection(db, this.collections.analytics), 
      orderBy('timestamp', 'desc')
    );
    return onSnapshot(q, (querySnapshot) => {
      if (!querySnapshot.empty) {
        const latestDoc = querySnapshot.docs[0];
        callback({ id: latestDoc.id, ...latestDoc.data() });
      }
    });
  }
}

// Export singleton instance
export const firestoreService = new FirestoreService();
```

### 3. Update `index.html` to include Firebase

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ganpati Donation & Expense Tracker</title>
    <link rel="stylesheet" href="css/style.css">
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
</head>
<body>
    <!-- Your existing HTML content -->
    
    <!-- Firebase SDK -->
    <script type="module">
        // Import Firebase modules
        import { initializeApp } from 'https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js';
        import { getFirestore } from 'https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js';
        
        // Your Firebase config
        const firebaseConfig = {
            // Replace with your actual config
            apiKey: "your-api-key",
            authDomain: "ganpati-tracker.firebaseapp.com",
            projectId: "ganpati-tracker",
            storageBucket: "ganpati-tracker.appspot.com",
            messagingSenderId: "123456789",
            appId: "your-app-id"
        };

        // Initialize Firebase
        const app = initializeApp(firebaseConfig);
        const db = getFirestore(app);
        
        // Make Firebase available globally
        window.firebaseApp = app;
        window.firestoreDb = db;
        
        console.log('Firebase initialized successfully');
    </script>

    <!-- Your existing scripts -->
    <script src="js/dataManager.js"></script>
    <script src="js/formValidator.js"></script>
    <script src="js/analyticsEngine.js"></script>
    <script src="js/dataMigration.js"></script>
    <script src="js/donationAPI.js"></script>
    <script src="js/main.js"></script>
</body>
</html>
```

### 4. Create `js/firebase-integration.js`

```javascript
// Firebase Integration for Ganpati Tracker
// This file integrates Firebase with existing functionality

class FirebaseIntegration {
    constructor() {
        this.isFirebaseReady = false;
        this.initializeFirebase();
    }

    async initializeFirebase() {
        try {
            // Wait for Firebase to be available
            while (!window.firebaseApp || !window.firestoreDb) {
                await new Promise(resolve => setTimeout(resolve, 100));
            }
            
            this.db = window.firestoreDb;
            this.isFirebaseReady = true;
            console.log('Firebase integration ready');
            
            // Initialize real-time listeners
            this.setupRealtimeListeners();
            
        } catch (error) {
            console.error('Firebase initialization failed:', error);
        }
    }

    // Setup real-time listeners for live updates
    setupRealtimeListeners() {
        if (!this.isFirebaseReady) return;

        // Import Firestore functions
        import('https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js')
            .then(({ collection, onSnapshot, query, orderBy }) => {
                
                // Listen to donations
                const donationsQuery = query(
                    collection(this.db, 'donations'), 
                    orderBy('timestamp', 'desc')
                );
                
                onSnapshot(donationsQuery, (snapshot) => {
                    const donations = [];
                    snapshot.forEach((doc) => {
                        donations.push({ id: doc.id, ...doc.data() });
                    });
                    this.updateDonationsUI(donations);
                });

                // Listen to expenses
                const expensesQuery = query(
                    collection(this.db, 'expenses'), 
                    orderBy('timestamp', 'desc')
                );
                
                onSnapshot(expensesQuery, (snapshot) => {
                    const expenses = [];
                    snapshot.forEach((doc) => {
                        expenses.push({ id: doc.id, ...doc.data() });
                    });
                    this.updateExpensesUI(expenses);
                });

                // Update analytics in real-time
                this.updateAnalyticsRealtime();
            });
    }

    // Add donation to Firebase
    async addDonation(donationData) {
        if (!this.isFirebaseReady) {
            console.warn('Firebase not ready, using local storage');
            return this.addDonationLocal(donationData);
        }

        try {
            const { collection, addDoc, serverTimestamp } = await import(
                'https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js'
            );

            const docRef = await addDoc(collection(this.db, 'donations'), {
                ...donationData,
                timestamp: serverTimestamp(),
                createdAt: new Date().toISOString()
            });

            console.log('Donation added to Firebase:', docRef.id);
            return docRef.id;

        } catch (error) {
            console.error('Error adding donation to Firebase:', error);
            // Fallback to local storage
            return this.addDonationLocal(donationData);
        }
    }

    // Add expense to Firebase
    async addExpense(expenseData) {
        if (!this.isFirebaseReady) {
            console.warn('Firebase not ready, using local storage');
            return this.addExpenseLocal(expenseData);
        }

        try {
            const { collection, addDoc, serverTimestamp } = await import(
                'https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js'
            );

            const docRef = await addDoc(collection(this.db, 'expenses'), {
                ...expenseData,
                timestamp: serverTimestamp(),
                createdAt: new Date().toISOString()
            });

            console.log('Expense added to Firebase:', docRef.id);
            return docRef.id;

        } catch (error) {
            console.error('Error adding expense to Firebase:', error);
            // Fallback to local storage
            return this.addExpenseLocal(expenseData);
        }
    }

    // Get all donations from Firebase
    async getDonations() {
        if (!this.isFirebaseReady) {
            return this.getDonationsLocal();
        }

        try {
            const { collection, getDocs, query, orderBy } = await import(
                'https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js'
            );

            const q = query(
                collection(this.db, 'donations'), 
                orderBy('timestamp', 'desc')
            );
            
            const querySnapshot = await getDocs(q);
            const donations = [];
            
            querySnapshot.forEach((doc) => {
                donations.push({ id: doc.id, ...doc.data() });
            });

            return donations;

        } catch (error) {
            console.error('Error getting donations from Firebase:', error);
            return this.getDonationsLocal();
        }
    }

    // Get all expenses from Firebase
    async getExpenses() {
        if (!this.isFirebaseReady) {
            return this.getExpensesLocal();
        }

        try {
            const { collection, getDocs, query, orderBy } = await import(
                'https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js'
            );

            const q = query(
                collection(this.db, 'expenses'), 
                orderBy('timestamp', 'desc')
            );
            
            const querySnapshot = await getDocs(q);
            const expenses = [];
            
            querySnapshot.forEach((doc) => {
                expenses.push({ id: doc.id, ...doc.data() });
            });

            return expenses;

        } catch (error) {
            console.error('Error getting expenses from Firebase:', error);
            return this.getExpensesLocal();
        }
    }

    // Update analytics in real-time
    async updateAnalyticsRealtime() {
        try {
            const donations = await this.getDonations();
            const expenses = await this.getExpenses();

            const totalDonations = donations.reduce((sum, donation) => 
                sum + parseFloat(donation.amount || 0), 0
            );
            
            const totalExpenses = expenses.reduce((sum, expense) => 
                sum + parseFloat(expense.amount || 0), 0
            );

            const balance = totalDonations - totalExpenses;
            const totalDonors = donations.length;

            // Update UI
            this.updateAnalyticsUI({
                totalDonations,
                totalExpenses,
                balance,
                totalDonors
            });

        } catch (error) {
            console.error('Error updating analytics:', error);
        }
    }

    // Update donations UI
    updateDonationsUI(donations) {
        // Update donations table/list in UI
        console.log('Updating donations UI:', donations);
        // Implement UI update logic here
    }

    // Update expenses UI
    updateExpensesUI(expenses) {
        // Update expenses table/list in UI
        console.log('Updating expenses UI:', expenses);
        // Implement UI update logic here
    }

    // Update analytics UI
    updateAnalyticsUI(analytics) {
        // Update analytics cards on homepage
        const totalDonationsEl = document.getElementById('total-donations');
        const totalExpensesEl = document.getElementById('total-expenses');
        const balanceEl = document.getElementById('balance');
        const totalDonorsEl = document.getElementById('total-donors');

        if (totalDonationsEl) totalDonationsEl.textContent = `₹${analytics.totalDonations.toLocaleString()}`;
        if (totalExpensesEl) totalExpensesEl.textContent = `₹${analytics.totalExpenses.toLocaleString()}`;
        if (balanceEl) balanceEl.textContent = `₹${analytics.balance.toLocaleString()}`;
        if (totalDonorsEl) totalDonorsEl.textContent = analytics.totalDonors;
    }

    // Fallback methods for local storage
    addDonationLocal(donationData) {
        const donations = JSON.parse(localStorage.getItem('donations') || '[]');
        const newDonation = {
            id: Date.now().toString(),
            ...donationData,
            createdAt: new Date().toISOString()
        };
        donations.push(newDonation);
        localStorage.setItem('donations', JSON.stringify(donations));
        return newDonation.id;
    }

    addExpenseLocal(expenseData) {
        const expenses = JSON.parse(localStorage.getItem('expenses') || '[]');
        const newExpense = {
            id: Date.now().toString(),
            ...expenseData,
            createdAt: new Date().toISOString()
        };
        expenses.push(newExpense);
        localStorage.setItem('expenses', JSON.stringify(expenses));
        return newExpense.id;
    }

    getDonationsLocal() {
        return JSON.parse(localStorage.getItem('donations') || '[]');
    }

    getExpensesLocal() {
        return JSON.parse(localStorage.getItem('expenses') || '[]');
    }
}

// Initialize Firebase integration
const firebaseIntegration = new FirebaseIntegration();

// Make it globally available
window.firebaseIntegration = firebaseIntegration;
```

## Step 5: Security Rules (Production)

When ready for production, update Firestore security rules:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Donations collection
    match /donations/{donationId} {
      allow read: if true; // Public read for transparency
      allow write: if request.auth != null; // Only authenticated users can write
    }
    
    // Expenses collection
    match /expenses/{expenseId} {
      allow read: if true; // Public read for transparency
      allow write: if request.auth != null; // Only authenticated users can write
    }
    
    // Analytics collection
    match /analytics/{analyticsId} {
      allow read: if true; // Public read
      allow write: if request.auth != null; // Only authenticated users can write
    }
    
    // Users collection
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

## Step 6: Usage Examples

### Adding a Donation
```javascript
// Add donation
const donationData = {
    donorName: 'John Doe',
    amount: 1000,
    email: 'john@example.com',
    phone: '+91-9876543210',
    address: 'Mumbai, Maharashtra',
    paymentMethod: 'UPI'
};

firebaseIntegration.addDonation(donationData);
```

### Adding an Expense
```javascript
// Add expense
const expenseData = {
    description: 'Decoration Materials',
    amount: 5000,
    category: 'Decorations',
    vendor: 'Local Vendor',
    date: new Date().toISOString()
};

firebaseIntegration.addExpense(expenseData);
```

### Getting Real-time Data
```javascript
// Get donations
firebaseIntegration.getDonations().then(donations => {
    console.log('All donations:', donations);
});

// Get expenses
firebaseIntegration.getExpenses().then(expenses => {
    console.log('All expenses:', expenses);
});
```

## Step 7: Testing

1. Open your website
2. Check browser console for "Firebase initialized successfully"
3. Try adding donations/expenses
4. Check Firebase Console to see data in Firestore
5. Verify real-time updates work across multiple browser tabs

## Benefits of This Integration

1. **Real-time Updates**: Changes appear instantly across all connected devices
2. **Scalability**: Firebase handles scaling automatically
3. **Offline Support**: Firebase provides offline capabilities
4. **Security**: Robust security rules and authentication
5. **Backup**: Automatic data backup and recovery
6. **Analytics**: Built-in analytics and monitoring

## Next Steps

1. Set up Firebase Authentication for user management
2. Implement data validation and error handling
3. Add offline support for better user experience
4. Set up Firebase Hosting for deployment
5. Configure backup and monitoring

This integration will provide your Ganpati Tracker with enterprise-level database capabilities while maintaining real-time synchronization across all users.