Here’s a detailed breakdown of the **User Authentication** process using the 3CX API, including how to use **Postman** to obtain an access token and make further authenticated API calls:

### Steps to Authenticate a User in 3CX

#### 1. **Create a User in the 3CX Admin Console**

- Log into your 3CX Admin Console.
- Navigate to the **Users** section.
- Create a new user, or use an existing one. 
- Make sure the **"Enable 2FA"** option is **disabled**.
- Assign an email address to the user, as a welcome email with a reset password link will be sent.

#### 2. **Reset Password for the User**

- Go to the email inbox of the user.
- Open the welcome email sent by 3CX, and click the **Reset Password** link.
- Set a new password for the Web Client login.

#### 3. **Obtain the User Token**

Once the user is created and the password is reset, you can now authenticate the user via the API. In this step, you'll get an access token and a refresh token.

**API Endpoint**:
```
POST /webclient/api/Login/GetAccessToken
```

**Headers**:
```
Accept: application/json
Content-Type: application/json
```

**Body** (replace `USER_NUM` and `USER_PASS` with the extension and password):
```json
{
  "SecurityCode": "",
  "Password": "USER_PASS",
  "Username": "USER_NUM"
}
```

#### 4. **Postman Setup**

Here’s how to set this up in **Postman**.

##### Step-by-Step Guide:

1. **Create a New Request**:
   - Open **Postman**.
   - Create a new **POST** request.
   - In the **Request URL**, enter:
     ```
     https://<your-3cx-server>:5001/webclient/api/Login/GetAccessToken
     ```

2. **Set Headers**:
   - In the **Headers** tab, add the following key-value pairs:
     ```
     Accept: application/json
     Content-Type: application/json
     ```

3. **Set Body**:
   - Switch to the **Body** tab, select **raw**, and choose **JSON** from the dropdown.
   - Enter the following JSON (replace `USER_NUM` and `USER_PASS` with the actual extension number and password for the user):
     ```json
     {
       "SecurityCode": "",
       "Password": "USER_PASS",
       "Username": "USER_NUM"
     }
     ```

4. **Send Request**:
   - Click **Send**.
   - If successful, you should receive a response with a status code `200 OK` and a body similar to this:
     ```json
     {
       "Status": "AuthSuccess",
       "Token": {
         "token_type": "Bearer",
         "expires_in": 60,
         "access_token": "ACCESS_TOKEN",
         "refresh_token": "REFRESH_TOKEN"
       },
       "TwoFactorAuth": null
     }
     ```

5. **Extract Access Token**:
   - Copy the `access_token` from the response and store it in Postman’s environment variables for future API requests.

#### 5. **Quick Test: Get 3CX Version**

Now that you have the access token, you can use it to test if your token works by making an authenticated request to check the 3CX version.

**API Endpoint**:
```
GET /xapi/v1/Defs?%24select=Id
```

**Headers**:
```
Authorization: Bearer ACCESS_TOKEN
```

**Postman Setup for Quick Test**:

1. **Create a New GET Request**:
   - Create a new **GET** request in Postman.
   - In the **Request URL**, enter:
     ```
     https://<your-3cx-server>:5001/xapi/v1/Defs?%24select=Id
     ```

2. **Set Headers**:
   - In the **Headers** tab, add:
     ```
     Authorization: Bearer ACCESS_TOKEN
     ```

3. **Send Request**:
   - Click **Send**.
   - If the access token is valid, you will receive a `200 OK` status in the response along with information about your 3CX system.

#### 6. **Handling Token Expiry**

The **access token** expires in 60 minutes. When it expires, you'll get a `401 Unauthorized` response when making API calls. To avoid re-authenticating manually every hour, you can use the **refresh token** to obtain a new access token without entering the username and password again.

### Example of Refreshing the Access Token

To refresh the access token, call the appropriate API endpoint using the refresh token:

**API Endpoint**:
```
POST /webclient/api/Login/RefreshAccessToken
```

**Headers**:
```
Content-Type: application/json
```

**Body**:
```json
{
  "refresh_token": "REFRESH_TOKEN"
}
```

### Conclusion

You now know how to:
- Create a user in 3CX for API authentication.
- Use Postman to make an API call to authenticate a user.
- Obtain an access token and make authenticated requests.
- Handle token expiration using refresh tokens.

This setup will allow you to interact with 3CX API for various operations within the user's permission scope.
