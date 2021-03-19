# Ruby Snippet for Bittrex API v3
Ruby Snippet to Connect to Bittrex API v3
```ruby
    API_URI = 'https://api.bittrex.com/v3'

    def self.get(path, data, params = {})
      uri = encode_uri(path, params)
      timestamp = (Time.now.to_f * 1000).to_i
      content_hash = digest
      presign = timestamp.to_s + uri + 'GET' + content_hash

      resp = Faraday.get(uri) do |req|
        req.params = params
        req.headers['Api-Key'] = data[:api_key]
        req.headers['Api-Timestamp'] = timestamp.to_s
        req.headers['Api-Content-Hash'] = content_hash
        req.headers['Api-Signature'] = OpenSSL::HMAC.hexdigest('sha512', data[:api_secret], presign)
        req.headers['Content-Type'] = 'application/json'
      end
      JSON.parse(resp.body, symbolize_names: true)
    end

    def self.encode_uri(path, params)
      uri = API_URI + '/' + path
      uri = uri + '?' + URI.encode_www_form(params) if params.any?
      uri
    end

    def self.digest(body = nil)
      string =
      if body.is_a?(Hash)
        body.to_json
      elsif body.nil?
        ''
      end
      Digest::SHA512.hexdigest(string)
    end
    
     deposits = get('deposits/closed', data) # to endpoint to get Deposit history
    
    
    
