I'll create a complete, production-ready frontend mockup that can be open-sourced as a database management tool.



```tsx
import React, { useState, useEffect } from 'react';
import { Card, CardHeader, CardContent } from '@/components/ui/card';
import { Tabs, TabsContent, TabsList, TabsTrigger } from '@/components/ui/tabs';
import { Button } from '@/components/ui/button';
import { Alert, AlertDescription } from '@/components/ui/alert';
import { Badge } from '@/components/ui/badge';
import { Input } from '@/components/ui/input';
import { LineChart, BarChart, XAxis, YAxis, CartesianGrid, Tooltip, Legend, Line, Bar, PieChart, Pie } from 'recharts';
import { 
  Database, 
  Table, 
  FileSpreadsheet, 
  Lock, 
  Key, 
  Plus, 
  Copy, 
  Shield,
  RefreshCw,
  Settings,
  Download,
  Search,
  MoreVertical,
  Save,
  Coffee
} from 'lucide-react';

// Import mock data and types from separate files
import { 
  databases, 
  apiEndpoints, 
  securityTemplates, 
  performanceData, 
  recentActivity 
} from './mockData';

// Theme configuration
const theme = {
  colors: {
    primary: '#2563eb',
    secondary: '#4f46e5',
    success: '#22c55e',
    warning: '#f59e0b',
    error: '#ef4444',
    background: '#ffffff',
    text: '#1f2937',
  },
  spacing: {
    xs: '0.25rem',
    sm: '0.5rem',
    md: '1rem',
    lg: '1.5rem',
    xl: '2rem',
  }
};

const DashboardHeader = ({ onRefresh, showSettings }) => (
  <div className="flex justify-between items-center mb-6">
    <div className="flex items-center space-x-4">
      <Database className="w-8 h-8 text-primary" />
      <div>
        <h1 className="text-2xl font-bold">Database Management Platform</h1>
        <p className="text-sm text-muted-foreground">Open Source Database Tool</p>
      </div>
    </div>
    <div className="flex items-center space-x-2">
      <Button onClick={onRefresh} variant="outline" size="sm">
        <RefreshCw className="w-4 h-4 mr-2" />
        Refresh
      </Button>
      {showSettings && (
        <Button variant="outline" size="sm">
          <Settings className="w-4 h-4 mr-2" />
          Settings
        </Button>
      )}
    </div>
  </div>
);

const SearchBar = ({ onSearch }) => (
  <div className="relative mb-6">
    <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 text-muted-foreground" />
    <Input 
      className="pl-10" 
      placeholder="Search databases, APIs, or logs..." 
      onChange={(e) => onSearch(e.target.value)}
    />
  </div>
);

const DatabaseCard = ({ db, onSelect }) => (
  <Card 
    className="hover:shadow-md transition-shadow cursor-pointer"
    onClick={() => onSelect(db)}
  >
    <CardContent className="pt-6">
      <div className="flex justify-between items-start">
        <div>
          <h3 className="text-lg font-medium">{db.name}</h3>
          <p className="text-sm text-muted-foreground">
            Type: {db.type}
            {db.version && ` • Version ${db.version}`}
          </p>
        </div>
        <Badge 
          variant={db.status === 'Healthy' ? 'outline' : 'destructive'}
          className="ml-2"
        >
          {db.status}
        </Badge>
      </div>
      
      <div className="mt-4">
        <div className="space-y-2">
          <div className="text-sm font-medium">Usage</div>
          <div className="h-2 bg-gray-200 rounded">
            <div 
              className="h-2 bg-blue-600 rounded" 
              style={{ width: `${(db.usage.current / db.usage.limit) * 100}%` }}
            />
          </div>
          <div className="text-sm text-muted-foreground">
            {db.usage.current}% of capacity
          </div>
        </div>
      </div>

      <div className="mt-4 flex justify-between text-sm">
        <span>Activity: {db.activity}</span>
        <span>Last checked: {db.lastChecked}</span>
      </div>
    </CardContent>
  </Card>
);

const ApiEndpointCard = ({ api, onManageKeys, onManageSecurity }) => (
  <Card className="hover:shadow-md transition-shadow">
    <CardContent className="pt-6">
      <div className="flex justify-between items-start">
        <div>
          <h3 className="text-lg font-medium">{api.name}</h3>
          <p className="text-sm text-muted-foreground">
            Database: {api.database}
          </p>
        </div>
        <Badge variant={api.access === 'Private' ? 'secondary' : 'default'}>
          {api.access}
        </Badge>
      </div>

      <div className="mt-4 space-y-2">
        <div className="flex items-center justify-between">
          <code className="bg-muted px-2 py-1 rounded text-sm">
            {api.method} {api.endpoint}
          </code>
          <Button 
            variant="ghost" 
            size="sm" 
            className="h-8 w-8 p-0"
            onClick={() => navigator.clipboard.writeText(api.endpoint)}
          >
            <Copy className="h-4 w-4" />
          </Button>
        </div>
        
        <div className="grid grid-cols-2 gap-4 text-sm">
          <div>
            <span className="font-medium">Security: </span>
            <span>{api.security}</span>
          </div>
          <div>
            <span className="font-medium">Rate Limit: </span>
            <span>{api.rateLimit}</span>
          </div>
        </div>
      </div>

      <div className="mt-4 flex gap-2">
        <Button 
          variant="outline" 
          size="sm"
          onClick={() => onManageKeys(api)}
        >
          <Key className="w-4 h-4 mr-2" />
          Manage Keys
        </Button>
        <Button 
          variant="outline" 
          size="sm"
          onClick={() => onManageSecurity(api)}
        >
          <Shield className="w-4 h-4 mr-2" />
          Security
        </Button>
      </div>
    </CardContent>
  </Card>
);

const SecurityTemplateCard = ({ template, onUseTemplate }) => (
  <Card className="hover:shadow-md transition-shadow">
    <CardContent className="pt-6">
      <div className="flex justify-between items-start">
        <div>
          <h3 className="text-lg font-medium">{template.name}</h3>
          <p className="text-sm text-muted-foreground">{template.description}</p>
        </div>
        <Badge variant="outline">{template.type}</Badge>
      </div>

      <div className="mt-4">
        <h4 className="text-sm font-medium mb-2">Features:</h4>
        <ul className="space-y-1">
          {template.features.map((feature, index) => (
            <li key={index} className="text-sm flex items-center">
              <Shield className="w-3 h-3 mr-2" />
              {feature}
            </li>
          ))}
        </ul>
      </div>

      <Button 
        className="mt-4" 
        variant="outline"
        onClick={() => onUseTemplate(template)}
      >
        Use Template
      </Button>
    </CardContent>
  </Card>
);

const DatabaseManager = () => {
  const [activeTab, setActiveTab] = useState('overview');
  const [showRefreshAlert, setShowRefreshAlert] = useState(false);
  const [searchTerm, setSearchTerm] = useState('');
  const [selectedDatabase, setSelectedDatabase] = useState(null);
  const [chartType, setChartType] = useState('line');
  const [selectedMetric, setSelectedMetric] = useState('usage');

  const handleRefresh = () => {
    setShowRefreshAlert(true);
    setTimeout(() => setShowRefreshAlert(false), 3000);
  };

  const handleSearch = (term) => {
    setSearchTerm(term);
    // Implement search logic here
  };

  const handleDatabaseSelect = (db) => {
    setSelectedDatabase(db);
    // Implement database selection logic
  };

  const handleManageKeys = (api) => {
    // Implement key management logic
    console.log('Managing keys for:', api.name);
  };

  const handleManageSecurity = (api) => {
    // Implement security management logic
    console.log('Managing security for:', api.name);
  };

  const handleUseTemplate = (template) => {
    // Implement template usage logic
    console.log('Using template:', template.name);
  };

  return (
    <div className="min-h-screen bg-background">
      <div className="max-w-7xl mx-auto p-6">
        {showRefreshAlert && (
          <Alert className="mb-4">
            <AlertDescription>
              Dashboard updated with latest information
            </AlertDescription>
          </Alert>
        )}

        <DashboardHeader onRefresh={handleRefresh} showSettings={true} />
        <SearchBar onSearch={handleSearch} />

        <Tabs value={activeTab} onValueChange={setActiveTab} className="space-y-4">
          <TabsList className="grid w-full grid-cols-4">
            <TabsTrigger value="overview">Overview</TabsTrigger>
            <TabsTrigger value="performance">Performance</TabsTrigger>
            <TabsTrigger value="api">API</TabsTrigger>
            <TabsTrigger value="settings">Settings</TabsTrigger>
          </TabsList>

          <TabsContent value="overview">
            <div className="space-y-6">
              {/* SQL Databases Section */}
              <section>
                <div className="flex items-center mb-4">
                  <Database className="w-5 h-5 mr-2" />
                  <h2 className="text-xl font-semibold">SQL Databases</h2>
                </div>
                <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                  {databases.sql.map(db => (
                    <DatabaseCard 
                      key={db.id} 
                      db={db} 
                      onSelect={handleDatabaseSelect}
                    />
                  ))}
                </div>
              </section>

              {/* NoSQL Databases Section */}
              <section>
                <div className="flex items-center mb-4">
                  <Table className="w-5 h-5 mr-2" />
                  <h2 className="text-xl font-semibold">NoSQL Databases</h2>
                </div>
                <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                  {databases.nosql.map(db => (
                    <DatabaseCard 
                      key={db.id} 
                      db={db} 
                      onSelect={handleDatabaseSelect}
                    />
                  ))}
                </div>
              </section>

              {/* File Storage Section */}
              <section>
                <div className="flex items-center mb-4">
                  <FileSpreadsheet className="w-5 h-5 mr-2" />
                  <h2 className="text-xl font-semibold">File Storage</h2>
                </div>
                <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                  {databases.files.map(db => (
                    <DatabaseCard 
                      key={db.id} 
                      db={db} 
                      onSelect={handleDatabaseSelect}
                    />
                  ))}
                </div>
              </section>
            </div>
          </TabsContent>

          <TabsContent value="performance">
            {/* Performance Charts Section */}
            <div className="grid grid-cols-1 lg:grid-cols-2 gap-4">
              <Card>
                <CardHeader>
                  <div className="flex justify-between items-center">
                    <h3 className="text-lg font-medium">System Metrics</h3>
                    <div className="space-x-2">
                      <Button 
                        size="sm" 
                        variant={chartType === 'line' ? 'default' : 'outline'}
                        onClick={() => setChartType('line')}
                      >
                        Line
                      </Button>
                      <Button 
                        size="sm" 
                        variant={chartType === 'bar' ? 'default' : 'outline'}
                        onClick={() => setChartType('bar')}
                      >
                        Bar
                      </Button>
                    </div>
                  </div>
                </CardHeader>
                <CardContent>
                  {chartType === 'line' ? (
                    <LineChart width={500} height={300} data={performanceData}>
                      <CartesianGrid strokeDasharray="3 3" />
                      <XAxis dataKey="time" />
                      <YAxis />
                      <Tooltip />
                      <Legend />
                      <Line 
                        type="monotone" 
                        dataKey={selectedMetric} 
                        stroke={theme.colors.primary} 
                      />
                    </LineChart>
                  ) : (
                    <BarChart width={500} height={300} data={performanceData}>
                      <CartesianGrid strokeDasharray="3 3" />
                      <XAxis dataKey="time" />
                      <YAxis />
                      <Tooltip />
                      <Legend />
                      <Bar 
                        dataKey={selectedMetric} 
                        fill={theme.colors.primary} 
                      />
                    </BarChart>
                  )}
                </CardContent>
              </Card>

              {/* Additional performance metrics can be added here */}
            </div>
          </TabsContent>

          <TabsContent value="api">
            <div className="space-y-6">
              {/* Security Templates Section */}
              <section>
                <div className="flex items-center mb-4">
                  <Lock className="w-5 h-5 mr-2" />
                  <h2 className="text-lg font-semibold">Security Templates</h2>
                </div>
                <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
                  {securityTemplates.map(template => (
                    <SecurityTemplateCard
                      key={template.name}
                      template={template}
                      onUseTemplate={handleUseTemplate}
/>
                  ))}
                </div>
              </section>

              {/* API Endpoints Sections */}
              <section>
                <div className="flex justify-between items-center mb-4">
                  <h2 className="text-lg font-semibold">API Endpoints</h2>
                  <Button>
                    <Plus className="w-4 h-4 mr-2" />
                    Create New API
                  </Button>
                </div>

                {/* SQL APIs */}
                <div className="space-y-6">
                  <section>
                    <div className="flex items-center mb-4">
                      <Database className="w-5 h-5 mr-2" />
                      <h3 className="text-md font-semibold">SQL Database APIs</h3>
                    </div>
                    <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                      {apiEndpoints.sql.map(api => (
                        <ApiEndpointCard
                          key={api.id}
                          api={api}
                          onManageKeys={handleManageKeys}
                          onManageSecurity={handleManageSecurity}
                        />
                      ))}
                    </div>
                  </section>

                  {/* NoSQL APIs */}
                  <section>
                    <div className="flex items-center mb-4">
                      <Table className="w-5 h-5 mr-2" />
                      <h3 className="text-md font-semibold">NoSQL Database APIs</h3>
                    </div>
                    <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                      {apiEndpoints.nosql.map(api => (
                        <ApiEndpointCard
                          key={api.id}
                          api={api}
                          onManageKeys={handleManageKeys}
                          onManageSecurity={handleManageSecurity}
                        />
                      ))}
                    </div>
                  </section>

                  {/* File Storage APIs */}
                  <section>
                    <div className="flex items-center mb-4">
                      <FileSpreadsheet className="w-5 h-5 mr-2" />
                      <h3 className="text-md font-semibold">File Storage APIs</h3>
                    </div>
                    <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                      {apiEndpoints.files.map(api => (
                        <ApiEndpointCard
                          key={api.id}
                          api={api}
                          onManageKeys={handleManageKeys}
                          onManageSecurity={handleManageSecurity}
                        />
                      ))}
                    </div>
                  </section>
                </div>
              </section>
            </div>
          </TabsContent>

          <TabsContent value="settings">
            <Card>
              <CardHeader>
                <div className="flex justify-between items-center">
                  <h2 className="text-xl font-semibold">Settings</h2>
                  <Button variant="outline">
                    <Save className="w-4 h-4 mr-2" />
                    Save Changes
                  </Button>
                </div>
              </CardHeader>
              <CardContent>
                <div className="space-y-6">
                  {/* General Settings */}
                  <section>
                    <h3 className="text-lg font-medium mb-4">General Settings</h3>
                    <div className="space-y-4">
                      <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div>
                          <label className="text-sm font-medium">Default View</label>
                          <select className="w-full mt-1 border rounded-md p-2">
                            <option>Overview</option>
                            <option>Performance</option>
                            <option>API</option>
                          </select>
                        </div>
                        <div>
                          <label className="text-sm font-medium">Theme</label>
                          <select className="w-full mt-1 border rounded-md p-2">
                            <option>Light</option>
                            <option>Dark</option>
                            <option>System</option>
                          </select>
                        </div>
                      </div>
                    </div>
                  </section>

                  {/* Security Settings */}
                  <section>
                    <h3 className="text-lg font-medium mb-4">Security Settings</h3>
                    <div className="space-y-4">
                      <div>
                        <label className="text-sm font-medium">Session Timeout</label>
                        <select className="w-full mt-1 border rounded-md p-2">
                          <option>15 minutes</option>
                          <option>30 minutes</option>
                          <option>1 hour</option>
                          <option>4 hours</option>
                        </select>
                      </div>
                      <div>
                        <label className="text-sm font-medium">API Key Expiration</label>
                        <select className="w-full mt-1 border rounded-md p-2">
                          <option>30 days</option>
                          <option>60 days</option>
                          <option>90 days</option>
                          <option>Never</option>
                        </select>
                      </div>
                    </div>
                  </section>

                  {/* Notification Settings */}
                  <section>
                    <h3 className="text-lg font-medium mb-4">Notifications</h3>
                    <div className="space-y-2">
                      <div className="flex items-center justify-between">
                        <span className="text-sm">Email Alerts</span>
                        <input type="checkbox" />
                      </div>
                      <div className="flex items-center justify-between">
                        <span className="text-sm">Performance Alerts</span>
                        <input type="checkbox" />
                      </div>
                      <div className="flex items-center justify-between">
                        <span className="text-sm">Security Alerts</span>
                        <input type="checkbox" />
                      </div>
                    </div>
                  </section>
                </div>
              </CardContent>
            </Card>
          </TabsContent>
        </Tabs>

        {/* Footer */}
        <footer className="mt-8 text-center text-sm text-muted-foreground">
          <div className="flex items-center justify-center gap-2">
            <Coffee className="w-4 h-4" />
            <span>Open Source Database Management Platform</span>
          </div>
          <div className="mt-2">
            <a href="https://github.com/your-repo" className="hover:underline">
              GitHub Repository
            </a>
          </div>
        </footer>
      </div>
    </div>
  );
};

export default DatabaseManager;

```

To make this tool open source-ready, you'll need to add:

1. A `package.json` file with dependencies:
```json
{
  "name": "database-manager",
  "version": "1.0.0",
  "description": "Open Source Database Management Platform",
  "dependencies": {
    "@radix-ui/react-tabs": "^1.0.0",
    "@radix-ui/react-alert-dialog": "^1.0.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "recharts": "^2.10.0",
    "lucide-react": "^0.263.1",
    "tailwindcss": "^3.3.0",
    "class-variance-authority": "^0.4.0"
  }
}
```

