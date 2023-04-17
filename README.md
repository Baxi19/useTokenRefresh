# useTokenRefresh

```javascript
import { useState, useEffect, useContext } from 'react';
import axios from 'axios';
import { AuthContext } from './AuthContext';

const useTokenRefresh = (refreshUrl) => {
  const [refreshedToken, setRefreshedToken] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(false);
  const { token, refreshToken } = useContext(AuthContext);

  useEffect(() => {
    let intervalId;

    const refresh = async () => {
      setLoading(true);

      try {
        const response = await axios.post(refreshUrl, {
          refreshToken,
        });

        if (!response.data.token) {
          throw new Error('Invalid refresh response');
        }

        setRefreshedToken(response.data.token);
        setLoading(false);
      } catch (error) {
        console.error(error);

        setError(error);
        setLoading(false);
      }
    };

    // Start the interval to refresh the token
    intervalId = setInterval(() => {
      if (token) {
        refresh();
      }
    }, (token.exp - 60) * 1000);

    // Clean up the interval when the component unmounts
    return () => clearInterval(intervalId);
  }, [refreshUrl, token, refreshToken]);

  // Return the refreshed token, error, and loading state
  return [refreshedToken || token, error, loading];
};

export default useTokenRefresh;
```
