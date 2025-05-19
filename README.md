# Login-Register



<!-- login.component.html -->
<div class="auth-container">
  <div class="auth-card">
    <div class="auth-header">
      <h2>Welcome Back</h2>
      <p>Login to access your dashboard</p>
    </div>
    
    <form [formGroup]="loginForm" (ngSubmit)="onSubmit()">
      <div class="form-group">
        <label for="email">Email</label>
        <input type="email" class="form-control" id="email" formControlName="email" placeholder="Enter your email">
        <div *ngIf="loginForm.get('email')?.invalid && loginForm.get('email')?.touched" class="error-message">
          Please enter a valid email
        </div>
      </div>
      
      <div class="form-group">
        <label for="password">Password</label>
        <input type="password" class="form-control" id="password" formControlName="password" placeholder="Enter your password">
        <div *ngIf="loginForm.get('password')?.invalid && loginForm.get('password')?.touched" class="error-message">
          Password is required
        </div>
      </div>
      
      <div class="form-group form-check">
        <input type="checkbox" class="form-check-input" id="rememberMe">
        <label class="form-check-label" for="rememberMe">Remember me</label>
      </div>
      
      <button type="submit" class="btn btn-primary btn-block" [disabled]="loginForm.invalid">Login</button>
      
      <div class="auth-footer">
        <p>Don't have an account? <a routerLink="/register">Register here</a></p>
        <a href="#" class="forgot-password">Forgot password?</a>
      </div>
    </form>
  </div>
  
  <div class="auth-illustration">
    <img src="assets/images/login-illustration.svg" alt="Login Illustration">
    <div class="illustration-text">
      <h3>Skill-Based Task Allocation</h3>
      <p>Optimize your team's productivity with intelligent task matching</p>
    </div>
  </div>
</div>






#login component ts file


// login.component.ts
import { Component, OnInit } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';
import { Router } from '@angular/router';
import { AuthService } from '../../services/auth.service';

@Component({
  selector: 'app-login',
  templateUrl: './login.component.html',
  styleUrls: ['./login.component.scss']
})
export class LoginComponent implements OnInit {
  loginForm: FormGroup;
  errorMessage: string = '';

  constructor(
    private fb: FormBuilder,
    private authService: AuthService,
    private router: Router
  ) {
    this.loginForm = this.fb.group({
      email: ['', [Validators.required, Validators.email]],
      password: ['', Validators.required]
    });
  }

  ngOnInit(): void {}

  onSubmit() {
    if (this.loginForm.valid) {
      this.authService.login(this.loginForm.value).subscribe({
        next: (response) => {
          const role = response.user.role.role_name;
          if (role === 'Admin') {
            this.router.navigate(['/admin']);
          } else if (role === 'Manager') {
            this.router.navigate(['/manager']);
          } else {
            this.router.navigate(['/user']);
          }
        },
        error: (err) => {
          this.errorMessage = err.error.message || 'Invalid credentials. Please try again.';
        }
      });
    }
  }
}




#Register 


<!-- register.component.html -->
<div class="auth-container">
  <div class="auth-card">
    <div class="auth-header">
      <h2>Create Account</h2>
      <p>Get started with your free account</p>
    </div>
    
    <form [formGroup]="registerForm" (ngSubmit)="onSubmit()">
      <div class="form-row">
        <div class="form-group col-md-6">
          <label for="firstName">First Name</label>
          <input type="text" class="form-control" id="firstName" formControlName="firstName" placeholder="John">
        </div>
        <div class="form-group col-md-6">
          <label for="lastName">Last Name</label>
          <input type="text" class="form-control" id="lastName" formControlName="lastName" placeholder="Doe">
        </div>
      </div>
      
      <div class="form-group">
        <label for="email">Email</label>
        <input type="email" class="form-control" id="email" formControlName="email" placeholder="john.doe@example.com">
      </div>
      
      <div class="form-group">
        <label for="password">Password</label>
        <input type="password" class="form-control" id="password" formControlName="password" placeholder="Create a password">
        <small class="form-text text-muted">Minimum 8 characters</small>
      </div>
      
      <div class="form-group">
        <label for="confirmPassword">Confirm Password</label>
        <input type="password" class="form-control" id="confirmPassword" formControlName="confirmPassword" placeholder="Confirm your password">
      </div>
      
      <div class="form-group">
        <label for="role">Role</label>
        <select class="form-control" id="role" formControlName="role">
          <option value="">Select your role</option>
          <option value="Employee">Team Member</option>
          <option value="Manager">Manager</option>
        </select>
      </div>
      
      <div class="form-group form-check">
        <input type="checkbox" class="form-check-input" id="terms" formControlName="terms">
        <label class="form-check-label" for="terms">I agree to the <a href="#">Terms of Service</a> and <a href="#">Privacy Policy</a></label>
      </div>
      
      <button type="submit" class="btn btn-primary btn-block" [disabled]="registerForm.invalid">Create Account</button>
      
      <div class="auth-footer">
        <p>Already have an account? <a routerLink="/login">Login here</a></p>
      </div>
    </form>
  </div>
  
  <div class="auth-illustration">
    <img src="assets/images/register-illustration.svg" alt="Register Illustration">
    <div class="illustration-text">
      <h3>Join Our Platform</h3>
      <p>Start optimizing your team's productivity today</p>
    </div>
  </div>
</div>




#Register ts file 


// register.component.ts
import { Component, OnInit } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';
import { Router } from '@angular/router';
import { AuthService } from '../../services/auth.service';

@Component({
  selector: 'app-register',
  templateUrl: './register.component.html',
  styleUrls: ['./register.component.scss']
})
export class RegisterComponent implements OnInit {
  registerForm: FormGroup;
  errorMessage: string = '';
  successMessage: string = '';

  constructor(
    private fb: FormBuilder,
    private authService: AuthService,
    private router: Router
  ) {
    this.registerForm = this.fb.group({
      firstName: ['', Validators.required],
      lastName: ['', Validators.required],
      email: ['', [Validators.required, Validators.email]],
      password: ['', [Validators.required, Validators.minLength(8)]],
      confirmPassword: ['', Validators.required],
      role: ['', Validators.required],
      terms: [false, Validators.requiredTrue]
    }, { validator: this.passwordMatchValidator });
  }

  ngOnInit(): void {}

  passwordMatchValidator(form: FormGroup) {
    return form.get('password')?.value === form.get('confirmPassword')?.value
      ? null : { mismatch: true };
  }

  onSubmit() {
    if (this.registerForm.valid) {
      const { confirmPassword, terms, ...userData } = this.registerForm.value;
      this.authService.register(userData).subscribe({
        next: (response) => {
          this.successMessage = 'Registration successful! Redirecting to login...';
          setTimeout(() => {
            this.router.navigate(['/login']);
          }, 2000);
        },
        error: (err) => {
          this.errorMessage = err.error.message || 'Registration failed. Please try again.';
        }
      });
    }
  }
}







#admin dashboard 


<div class="dashboard-container">
  <app-navbar [user]="currentUser"></app-navbar>
  
  <div class="container-fluid">
    <div class="row">
      <app-sidebar [user]="currentUser"></app-sidebar>
      
      <main class="col-md-9 ms-sm-auto col-lg-10 px-md-4">
        <div class="d-flex justify-content-between flex-wrap flex-md-nowrap align-items-center pt-3 pb-2 mb-3 border-bottom">
          <h1 class="h2">Admin Dashboard</h1>
        </div>
        
        <div class="row">
          <!-- User Management -->
          <div class="col-md-12 mb-4">
            <div class="card h-100">
              <div class="card-header bg-primary text-white d-flex justify-content-between align-items-center">
                <h5 class="mb-0">User Management</h5>
                <button class="btn btn-light btn-sm" (click)="openUserModal()">
                  <i class="bi bi-plus-circle"></i> Add User
                </button>
              </div>
              <div class="card-body">
                <div class="table-responsive">
                  <table class="table table-striped table-hover">
                    <thead>
                      <tr>
                        <th>Name</th>
                        <th>Email</th>
                        <th>Role</th>
                        <th>Manager</th>
                        <th>Actions</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr *ngFor="let user of users">
                        <td>{{ user.name }}</td>
                        <td>{{ user.email }}</td>
                        <td>{{ user.role?.roleName }}</td>
                        <td>{{ user.manager?.name || 'None' }}</td>
                        <td>
                          <button class="btn btn-sm btn-outline-primary me-2" (click)="editUser(user)">
                            <i class="bi bi-pencil"></i>
                          </button>
                          <button class="btn btn-sm btn-outline-danger" (click)="deleteUser(user.userId)">
                            <i class="bi bi-trash"></i>
                          </button>
                        </td>
                      </tr>
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          </div>
          
          <!-- Role Management -->
          <div class="col-md-6 mb-4">
            <div class="card h-100">
              <div class="card-header bg-success text-white d-flex justify-content-between align-items-center">
                <h5 class="mb-0">Role Management</h5>
                <button class="btn btn-light btn-sm" (click)="openRoleModal()">
                  <i class="bi bi-plus-circle"></i> Add Role
                </button>
              </div>
              <div class="card-body">
                <div class="table-responsive">
                  <table class="table table-striped table-hover">
                    <thead>
                      <tr>
                        <th>Role Name</th>
                        <th>Actions</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr *ngFor="let role of roles">
                        <td>{{ role.roleName }}</td>
                        <td>
                          <button class="btn btn-sm btn-outline-primary me-2" (click)="editRole(role)">
                            <i class="bi bi-pencil"></i>
                          </button>
                          <button class="btn btn-sm btn-outline-danger" (click)="deleteRole(role.roleId)">
                            <i class="bi bi-trash"></i>
                          </button>
                        </td>
                      </tr>
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          </div>
          
          <!-- Permission Management -->
          <div class="col-md-6 mb-4">
            <div class="card h-100">
              <div class="card-header bg-info text-white d-flex justify-content-between align-items-center">
                <h5 class="mb-0">Permission Management</h5>
                <button class="btn btn-light btn-sm" (click)="openPermissionModal()">
                  <i class="bi bi-plus-circle"></i> Add Permission
                </button>
              </div>
              <div class="card-body">
                <div class="table-responsive">
                  <table class="table table-striped table-hover">
                    <thead>
                      <tr>
                        <th>Permission Name</th>
                        <th>Actions</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr *ngFor="let permission of permissions">
                        <td>{{ permission.permissionName }}</td>
                        <td>
                          <button class="btn btn-sm btn-outline-primary me-2" (click)="editPermission(permission)">
                            <i class="bi bi-pencil"></i>
                          </button>
                          <button class="btn btn-sm btn-outline-danger" (click)="deletePermission(permission.permissionId)">
                            <i class="bi bi-trash"></i>
                          </button>
                        </td>
                      </tr>
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          </div>
        </div>
        
        <!-- Role-Permission Assignment -->
        <div class="card mb-4">
          <div class="card-header bg-warning text-white">
            <h5 class="mb-0">Role-Permission Assignment</h5>
          </div>
          <div class="card-body">
            <div class="row">
              <div class="col-md-4 mb-3">
                <label for="roleSelect" class="form-label">Select Role</label>
                <select class="form-select" id="roleSelect" [(ngModel)]="selectedRoleId" (change)="onRoleChange()">
                  <option *ngFor="let role of roles" [value]="role.roleId">{{ role.roleName }}</option>
                </select>
              </div>
            </div>
            
            <div class="row" *ngIf="selectedRoleId">
              <div class="col-md-12">
                <h6>Assign Permissions</h6>
                <div class="form-check" *ngFor="let permission of permissions">
                  <input class="form-check-input" type="checkbox" [id]="'perm-' + permission.permissionId"
                         [(ngModel)]="permission.assigned" (change)="togglePermission(permission.permissionId)">
                  <label class="form-check-label" [for]="'perm-' + permission.permissionId">
                    {{ permission.permissionName }}
                  </label>
                </div>
                <button class="btn btn-primary mt-3" (click)="saveRolePermissions()">Save Assignments</button>
              </div>
            </div>
          </div>
        </div>
      </main>
    </div>
  </div>
</div>

<!-- User Modal -->
<app-user-modal *ngIf="showUserModal" [user]="selectedUser" [roles]="roles" [users]="users" 
                (close)="handleUserModalClose($event)"></app-user-modal>

<!-- Role Modal -->
<app-role-modal *ngIf="showRoleModal" [role]="selectedRole" (close)="handleRoleModalClose($event)"></app-role-modal>

<!-- Permission Modal -->
<app-permission-modal *ngIf="showPermissionModal" [permission]="selectedPermission" 
                      (close)="handlePermissionModalClose($event)"></app-permission-modal>





#manager dashboard

<div class="dashboard-container">
  <app-navbar [user]="currentUser"></app-navbar>
  
  <div class="container-fluid">
    <div class="row">
      <app-sidebar [user]="currentUser"></app-sidebar>
      
      <main class="col-md-9 ms-sm-auto col-lg-10 px-md-4">
        <div class="d-flex justify-content-between flex-wrap flex-md-nowrap align-items-center pt-3 pb-2 mb-3 border-bottom">
          <h1 class="h2">Manager Dashboard</h1>
          <div class="btn-toolbar mb-2 mb-md-0">
            <div class="btn-group me-2">
              <button class="btn btn-primary" (click)="openTaskModal()">
                <i class="bi bi-plus-circle"></i> Create Task
              </button>
              <button class="btn btn-success" (click)="openSkillModal()">
                <i class="bi bi-tools"></i> Add Skill
              </button>
            </div>
          </div>
        </div>
        
        <!-- Task Management -->
        <div class="card mb-4">
          <div class="card-header bg-primary text-white">
            <h5 class="mb-0">Task Management</h5>
          </div>
          <div class="card-body">
            <div class="table-responsive">
              <table class="table table-striped table-hover">
                <thead>
                  <tr>
                    <th>Title</th>
                    <th>Assigned To</th>
                    <th>Required Skills</th>
                    <th>Deadline</th>
                    <th>Status</th>
                    <th>Actions</th>
                  </tr>
                </thead>
                <tbody>
                  <tr *ngFor="let task of tasks">
                    <td>{{ task.title }}</td>
                    <td>
                      <span *ngIf="task.assignedTo">
                        {{ task.assignedTo.name }}
                      </span>
                      <span *ngIf="!task.assignedTo" class="text-muted">Unassigned</span>
                    </td>
                    <td>
                      <span class="badge bg-secondary me-1" *ngFor="let skill of task.requiredSkills">
                        {{ skill.skillName }}
                      </span>
                    </td>
                    <td>{{ task.deadline | date:'mediumDate' }}</td>
                    <td>
                      <span class="badge" [ngClass]="{
                        'bg-success': task.status === 'COMPLETED',
                        'bg-warning': task.status === 'IN_PROGRESS',
                        'bg-danger': task.status === 'OVERDUE',
                        'bg-info': task.status === 'PENDING'
                      }">
                        {{ task.status | titlecase }}
                      </span>
                    </td>
                    <td>
                      <button class="btn btn-sm btn-outline-primary me-2" (click)="editTask(task)">
                        <i class="bi bi-pencil"></i>
                      </button>
                      <button class="btn btn-sm btn-outline-danger" (click)="deleteTask(task.taskId)">
                        <i class="bi bi-trash"></i>
                      </button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
        
        <!-- Skill Management -->
        <div class="card mb-4">
          <div class="card-header bg-success text-white">
            <h5 class="mb-0">Skill Management</h5>
          </div>
          <div class="card-body">
            <div class="table-responsive">
              <table class="table table-striped table-hover">
                <thead>
                  <tr>
                    <th>Skill Name</th>
                    <th>Description</th>
                    <th>Category</th>
                    <th>Actions</th>
                  </tr>
                </thead>
                <tbody>
                  <tr *ngFor="let skill of skills">
                    <td>{{ skill.skillName }}</td>
                    <td>{{ skill.skillDescription }}</td>
                    <td>{{ skill.category }}</td>
                    <td>
                      <button class="btn btn-sm btn-outline-primary me-2" (click)="editSkill(skill)">
                        <i class="bi bi-pencil"></i>
                      </button>
                      <button class="btn btn-sm btn-outline-danger" (click)="deleteSkill(skill.skillId)">
                        <i class="bi bi-trash"></i>
                      </button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
        
        <!-- Team Members -->
        <div class="card mb-4">
          <div class="card-header bg-info text-white">
            <h5 class="mb-0">Team Members</h5>
          </div>
          <div class="card-body">
            <div class="table-responsive">
              <table class="table table-striped table-hover">
                <thead>
                  <tr>
                    <th>Name</th>
                    <th>Email</th>
                    <th>Skills</th>
                    <th>Current Tasks</th>
                  </tr>
                </thead>
                <tbody>
                  <tr *ngFor="let member of teamMembers">
                    <td>{{ member.user.name }}</td>
                    <td>{{ member.user.email }}</td>
                    <td>
                      <span class="badge bg-secondary me-1" *ngFor="let skill of member.skills">
                        {{ skill.skillName }}
                      </span>
                    </td>
                    <td>
                      <span class="badge bg-primary me-1" *ngFor="let task of member.tasks">
                        {{ task.title }}
                      </span>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
        
        <!-- Reports -->
        <div class="card">
          <div class="card-header bg-warning text-white">
            <h5 class="mb-0">Team Performance Reports</h5>
          </div>
          <div class="card-body">
            <div class="row mb-3">
              <div class="col-md-4">
                <label for="reportType" class="form-label">Report Type</label>
                <select class="form-select" id="reportType" [(ngModel)]="reportType">
                  <option value="TASK_COMPLETION">Task Completion</option>
                  <option value="SKILL_COVERAGE">Skill Coverage</option>
                  <option value="WORKLOAD">Workload Distribution</option>
                </select>
              </div>
              <div class="col-md-4">
                <label for="dateFrom" class="form-label">From</label>
                <input type="date" class="form-control" id="dateFrom" [(ngModel)]="dateFrom">
              </div>
              <div class="col-md-4">
                <label for="dateTo" class="form-label">To</label>
                <input type="date" class="form-control" id="dateTo" [(ngModel)]="dateTo">
              </div>
            </div>
            <button class="btn btn-primary" (click)="generateReport()">
              <i class="bi bi-file-earmark-bar-graph"></i> Generate Report
            </button>
            
            <div class="mt-4" *ngIf="reportData">
              <h5>Report Results</h5>
              <div class="chart-container" style="height: 400px;">
                <canvas id="reportChart"></canvas>
              </div>
              <button class="btn btn-outline-secondary mt-3" (click)="exportReport()">
                <i class="bi bi-download"></i> Export to Excel
              </button>
            </div>
          </div>
        </div>
      </main>
    </div>
  </div>
</div>

<!-- Task Modal -->
<app-task-modal *ngIf="showTaskModal" [task]="selectedTask" [skills]="skills" [users]="teamMembers" 
                (close)="handleTaskModalClose($event)"></app-task-modal>

<!-- Skill Modal -->
<app-skill-modal *ngIf="showSkillModal" [skill]="selectedSkill" (close)="handleSkillModalClose($event)"></app-skill-modal>




#user dashboard 


<div class="dashboard-container">
  <app-navbar [user]="currentUser"></app-navbar>
  
  <div class="container-fluid">
    <div class="row">
      <app-sidebar [user]="currentUser"></app-sidebar>
      
      <main class="col-md-9 ms-sm-auto col-lg-10 px-md-4">
        <div class="d-flex justify-content-between flex-wrap flex-md-nowrap align-items-center pt-3 pb-2 mb-3 border-bottom">
          <h1 class="h2">My Dashboard</h1>
          <div class="btn-toolbar mb-2 mb-md-0">
            <button class="btn btn-primary" (click)="openSkillSelectionModal()">
              <i class="bi bi-tools"></i> Update My Skills
            </button>
          </div>
        </div>
        
        <!-- My Tasks -->
        <div class="card mb-4">
          <div class="card-header bg-primary text-white">
            <h5 class="mb-0">My Tasks</h5>
          </div>
          <div class="card-body">
            <div class="table-responsive">
              <table class="table table-striped table-hover">
                <thead>
                  <tr>
                    <th>Title</th>
                    <th>Status</th>
                    <th>Deadline</th>
                    <th>Progress</th>
                    <th>Actions</th>
                  </tr>
                </thead>
                <tbody>
                  <tr *ngFor="let task of myTasks">
                    <td>{{ task.title }}</td>
                    <td>
                      <span class="badge" [ngClass]="{
                        'bg-success': task.status === 'COMPLETED',
                        'bg-warning': task.status === 'IN_PROGRESS',
                        'bg-danger': task.status === 'OVERDUE',
                        'bg-info': task.status === 'PENDING'
                      }">
                        {{ task.status | titlecase }}
                      </span>
                    </td>
                    <td>{{ task.deadline | date:'mediumDate' }}</td>
                    <td>
                      <div class="progress">
                        <div class="progress-bar" 
                             [ngClass]="{
                               'bg-success': task.progress === 100,
                               'bg-warning': task.progress < 100 && task.progress > 0,
                               'bg-danger': task.progress === 0
                             }"
                             [style.width]="task.progress + '%'">
                          {{ task.progress }}%
                        </div>
                      </div>
                    </td>
                    <td>
                      <button class="btn btn-sm btn-outline-primary me-2" (click)="updateTaskProgress(task)">
                        <i class="bi bi-arrow-up-circle"></i> Update
                      </button>
                      <button class="btn btn-sm btn-outline-info" (click)="viewTaskDetails(task)">
                        <i class="bi bi-info-circle"></i> Details
                      </button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
        
        <!-- Available Tasks -->
        <div class="card mb-4">
          <div class="card-header bg-success text-white">
            <h5 class="mb-0">Available Tasks (Matching Your Skills)</h5>
          </div>
          <div class="card-body">
            <div class="table-responsive">
              <table class="table table-striped table-hover">
                <thead>
                  <tr>
                    <th>Title</th>
                    <th>Required Skills</th>
                    <th>Deadline</th>
                    <th>Priority</th>
                    <th>Actions</th>
                  </tr>
                </thead>
                <tbody>
                  <tr *ngFor="let task of availableTasks">
                    <td>{{ task.title }}</td>
                    <td>
                      <span class="badge bg-secondary me-1" *ngFor="let skill of task.requiredSkills">
                        {{ skill.skillName }}
                      </span>
                    </td>
                    <td>{{ task.deadline | date:'mediumDate' }}</td>
                    <td>
                      <span class="badge" [ngClass]="{
                        'bg-danger': task.priority === 'HIGH',
                        'bg-warning': task.priority === 'MEDIUM',
                        'bg-success': task.priority === 'LOW'
                      }">
                        {{ task.priority | titlecase }}
                      </span>
                    </td>
                    <td>
                      <button class="btn btn-sm btn-outline-primary" (click)="requestTask(task.taskId)">
                        <i class="bi bi-hand-thumbs-up"></i> Request Task
                      </button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
        
        <!-- My Skills -->
        <div class="card">
          <div class="card-header bg-info text-white">
            <h5 class="mb-0">My Skills</h5>
          </div>
          <div class="card-body">
            <div class="row">
              <div class="col-md-4 mb-3" *ngFor="let skill of mySkills">
                <div class="card h-100">
                  <div class="card-body">
                    <h5 class="card-title">{{ skill.skillName }}</h5>
                    <p class="card-text">{{ skill.skillDescription }}</p>
                    <div class="d-flex justify-content-between align-items-center">
                      <div class="skill-level">
                        <span class="text-muted">Proficiency:</span>
                        <div class="stars">
                          <i class="bi bi-star-fill text-warning" *ngFor="let star of [1,2,3,4,5]; let i = index"
                              [class.text-warning]="i < skill.proficiencyLevel"
                              [class.text-muted]="i >= skill.proficiencyLevel"></i>
                        </div>
                      </div>
                      <button class="btn btn-sm btn-outline-danger" (click)="removeSkill(skill.skillId)">
                        <i class="bi bi-trash"></i>
                      </button>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </main>
    </div>
  </div>
</div>

<!-- Task Progress Modal -->
<app-task-progress-modal *ngIf="showProgressModal" [task]="selectedTask" (close)="handleProgressModalClose($event)"></app-task-progress-modal>

<!-- Skill Selection Modal -->
<app-skill-selection-modal *ngIf="showSkillSelectionModal" [userSkills]="mySkills" [allSkills]="allSkills" 
                          (close)="handleSkillSelectionModalClose($event)"></app-skill-selection-modal>

<!-- Task Details Modal -->
<app-task-details-modal *ngIf="showTaskDetailsModal" [task]="selectedTask" (close)="handleTaskDetailsModalClose()"></app-task-details-modal>





# navbar of landing page



 <nav class="navbar navbar-expand navbar-dark bg-dark fixed-top">
  <div class="container-fluid">
    <a class="navbar-brand" routerLink="/">
      <i class="bi bi-diagram-3-fill me-2"></i>SkillSync
    </a>
    
    <div class="collapse navbar-collapse">
      <ul class="navbar-nav me-auto">
        <li class="nav-item" *ngIf="user?.roles?.includes('ROLE_ADMIN')">
          <a class="nav-link" routerLink="/admin">Admin</a>
        </li>
        <li class="nav-item" *ngIf="user?.roles?.includes('ROLE_MANAGER')">
          <a class="nav-link" routerLink="/manager">Manager</a>
        </li>
        <li class="nav-item" *ngIf="user?.roles?.includes('ROLE_USER')">
          <a class="nav-link" routerLink="/user">My Dashboard</a>
        </li>
      </ul>
      
      <ul class="navbar-nav">
        <ng-container *ngIf="!isLoggedIn">
          <li class="nav-item">
            <a class="nav-link" routerLink="/login">Login</a>
          </li>
          <li class="nav-item">
            <a class="nav-link" routerLink="/register">Register</a>
          </li>
        </ng-container>
        
        <ng-container *ngIf="isLoggedIn">
          <li class="nav-item dropdown">
            <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-bs-toggle="dropdown">
              <i class="bi bi-person-circle me-1"></i>
              {{ user?.firstName }} {{ user?.lastName }}
            </a>
            <ul class="dropdown-menu dropdown-menu-end">
              <li><a class="dropdown-item" routerLink="/profile"><i class="bi bi-person me-2"></i>Profile</a></li>
              <li><hr class="dropdown-divider"></li>
              <li><a class="dropdown-item" (click)="logout()"><i class="bi bi-box-arrow-right me-2"></i>Logout</a></li>
            </ul>
          </li>
        </ng-container>
      </ul>
    </div>
  </div>
</nav>


# ts file 

import { Component, OnInit } from '@angular/core';
import { AuthService } from '../../services/auth.service';
import { Router } from '@angular/router';
import { ToastrService } from 'ngx-toastr';





@Component({
  selector: 'app-navbar',
  templateUrl: './navbar.component.html',
  styleUrls: ['./navbar.component.scss']
})
export class NavbarComponent implements OnInit {
  isLoggedIn = false;
  user: any;

  constructor(
    private authService: AuthService,
    private router: Router,
    private toastr: ToastrService
  ) {}

  ngOnInit(): void {
    this.isLoggedIn = this.authService.isLoggedIn();
    if (this.isLoggedIn) {
      this.user = this.authService.currentUserValue;
    }
    
    // Listen for authentication changes
    this.authService.authStatus.subscribe(status => {
      this.isLoggedIn = status;
      if (status) {
        this.user = this.authService.currentUserValue;
      } else {
        this.user = null;
      }
    });
  }

  logout(): void {
    this.authService.logout();
    this.toastr.success('You have been logged out successfully');
    this.router.navigate(['/login']);
  }
}



# css file: 


.navbar {
  padding: 0.75rem 1rem;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease;
  
  .navbar-brand {
    font-weight: 600;
    font-size: 1.25rem;
    
    i {
      font-size: 1.1em;
    }
  }
  
  .nav-link {
    padding: 0.5rem 1rem;
    font-weight: 500;
    
    &.active {
      color: rgba(255, 255, 255, 0.9);
    }
  }
  
  .dropdown-menu {
    border: none;
    box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.15);
    
    .dropdown-item {
      padding: 0.5rem 1.5rem;
      
      i {
        width: 1.25em;
        text-align: center;
      }
      
      &:hover {
        background-color: #f8f9fa;
        color: #212529;
      }
    }
  }
  
  @media (max-width: 767.98px) {
    .navbar-collapse {
      padding: 1rem 0;
    }
    
    .nav-link {
      padding: 0.5rem 0;
    }
  }
}
