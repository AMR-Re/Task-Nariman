Here's your task description and the instructions for uploading it to GitHub formatted in Markdown:

```markdown
# Code Selling Feature Implementation

Hi **Nariman AlHajie** 👋,

Below is the task breakdown for implementing the code selling feature on the website. You have **5 days** to complete the task, and we’re excited to see your progress! Let’s get started. 💻✨

---

### **1. Set Up Laravel Project** ⚙️ VERSION 11.XX
- **Set up a new Laravel project** or use an existing one.
- **Install necessary dependencies** for file upload, such as:
  - `laravel/filemanager` for file management.
  - `spatie/laravel-medialibrary` for storing and managing files.

---

### **2. Database Design & Model Creation** 🗃️
- **Create tables and models** for Files, Products, Purchases, and Transactions.

```php
// Migration for Files
Schema::create('files', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->string('description');
    $table->decimal('price', 8, 2);
    $table->timestamps();
});

// Model for File
class File extends Model {
    protected $fillable = ['name', 'description', 'price'];
}

// Migration for Products (linking files to products)
Schema::create('products', function (Blueprint $table) {
    $table->id();
    $table->foreignId('file_id')->constrained()->onDelete('cascade');
    $table->string('title');
    $table->timestamps();
});

// Model for Product
class Product extends Model {
    protected $fillable = ['file_id', 'title'];
}
```

---

### **3. File Upload and Management** 📁
- **File upload functionality** for code files (e.g., `.zip`, `.js`).
- **File validation** and **secure storage** (using Laravel’s Storage system).

```php
// File Upload Controller
public function upload(Request $request)
{
    $validated = $request->validate([
        'file' => 'required|file|mimes:zip,js|max:5000',
    ]);

    $path = $request->file('file')->store('files', 'public'); // Secure storage
    File::create([
        'name' => $request->file('file')->getClientOriginalName(),
        'description' => $request->input('description'),
        'price' => $request->input('price'),
        'path' => $path,
    ]);
}
```

---

### **4. CRUD Operations for Code Files** 🔧
- **CRUD operations** for the admin to manage files.

```php
// Admin Controller for Files
public function store(Request $request)
{
    $file = File::create($request->all());
    return redirect()->route('admin.files.index');
}

public function update(Request $request, File $file)
{
    $file->update($request->all());
    return redirect()->route('admin.files.index');
}

public function destroy(File $file)
{
    $file->delete();
    return redirect()->route('admin.files.index');
}
```

---

### **5. Stripe Integration for Payments** 💳
- **Stripe Checkout Integration** for purchasing files.

```php
// Stripe Controller
use Stripe\Stripe;
use Stripe\Checkout\Session;

public function createCheckoutSession(Request $request)
{
    Stripe::setApiKey(env('STRIPE_SECRET'));

    $checkoutSession = Session::create([
        'payment_method_types' => ['card'],
        'line_items' => [[
            'price_data' => [
                'currency' => 'usd',
                'product_data' => [
                    'name' => 'Code File',
                ],
                'unit_amount' => 1000, // Price in cents
            ],
            'quantity' => 1,
        ]],
        'mode' => 'payment',
        'success_url' => route('payment.success'),
        'cancel_url' => route('payment.cancel'),
    ]);

    return redirect($checkoutSession->url);
}
```

---

### **6. File Access Management** 🔒
- **Generate secure one-time download link** for users after successful purchase.

```php
// Generate signed URL for file download
public function downloadFile(File $file)
{
    return Storage::disk('public')->download($file->path);
}
```

---

### **7. User Dashboard** 🧑‍💻
- **Create a user dashboard** to view and download purchased files.

```php
// User Dashboard Controller
public function dashboard()
{
    $user = auth()->user();
    $purchasedFiles = $user->purchases; // Retrieve files the user has purchased
    return view('dashboard', compact('purchasedFiles'));
}
```

---

### **8. Transaction History and Receipts** 📜
- **Show purchase history** and allow users to download receipts.

```php
// Transaction History Controller
public function transactionHistory()
{
    $transactions = auth()->user()->transactions;
    return view('transaction-history', compact('transactions'));
}

// Generate receipt PDF
public function generateReceipt($transactionId)
{
    $transaction = Transaction::findOrFail($transactionId);
    $pdf = PDF::loadView('receipt', ['transaction' => $transaction]);
    return $pdf->download('receipt.pdf');
}
```

---

### **9. Testing and Validation** 🧪
- **Write unit tests** to verify file upload, payments, file access, and CRUD operations.

```php
// File Upload Test
public function testFileUpload()
{
    $response = $this->post('/upload', [
        'file' => UploadedFile::fake()->create('test.zip', 1024),
        'description' => 'Test File',
        'price' => 100,
    ]);

    $response->assertStatus(200);
    $this->assertDatabaseHas('files', ['name' => 'test.zip']);
}
```

---

### **10. Security & Access Control** 🔐
- **Authorization checks** to manage roles and ensure proper file access.

```php
// Middleware for file access control
public function handle($request, Closure $next)
{
    if (auth()->user()->cannot('access', File::class)) {
        return response()->json(['error' => 'Unauthorized'], 403);
    }

    return $next($request);
}
```

---

### **Task Provided by:**
**AMR REZK**  
Backend Engineer at **Link Leap**
If you need any clarifications or assistance, please feel free to reach out. Good luck, and we’re looking forward to seeing your great work! 🏆

⚠️ Important: All the code snippets provided are just examples. You are required to write your own base code.
---

© **2024 Link Leap**. All rights reserved.

---

# Uploading Your Task to GitHub

Once you have completed your task, follow these steps to upload your work to your GitHub repository.

## 1. Ensure All Changes Are Saved Locally

Before uploading, make sure that all your task files (including `.md` files and any related code files) are saved in your project directory.

## 2. Add Files to Git

If you haven’t already added your files to the Git repository, use the following command to add them to the staging area:

```bash
git add guide.md
```

If you want to add all modified and new files at once, use:

```bash
git add .
```

This command stages all changes, preparing them for the commit.

## 3. Commit Your Changes

Once you’ve added the files, commit your changes. A good commit message describes what was done. For example:

```bash
git commit -m "Complete task for code selling feature implementation"
```

This step saves your changes in your local Git history.

## 4. Push Changes to GitHub

Next, push your changes to the GitHub repository. If you’re working on the `main` branch:

```bash
git push origin main
```

If you're working on a feature branch, for example `feature-code-selling`, use:

```bash
git push origin feature-code-selling
```

This uploads your local commits to GitHub.

## 5. Create a Pull Request (if using a feature branch)

If you’re working on a separate feature branch (and not directly on the `main` branch), you need to create a **Pull Request (PR)**. Follow these steps:

1. Go to your GitHub repository.
2. Click **Compare & Pull Request** after pushing your branch.
3. Provide a description for the PR and submit it.

## 6. Review and Merge (if needed)

Once the PR is created, review the changes and merge the PR into the `main` branch (if using a separate branch).

- You or your team can review the PR to ensure everything is correct.
- After the review, click **Merge Pull Request** and confirm the merge.

## 7. Verify the Upload

Once the changes are merged (if using a separate branch), check the relevant folder in your GitHub repository (e.g., `docs/` or `guides/`). Make sure your files, including the `.md` file, are visible and properly formatted.

---
```

