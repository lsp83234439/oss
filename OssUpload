<?php

class OssUpload
{

    protected $config = array();

    // 上传文件
    public function uploadFile($localFile,$ext)
    {
        $accessKeyId = $this->config['accessKeyId'];
        $secretAccessKey = $this->config['accessKeySecret'];
        $bucket = $this->config['bucket'];
        $object = date('Y/m/d') . md5(time()). '.' . $ext;
        $endpoint = $this->config['endpoint'];

        $date = gmdate('D, d M Y H:i:s \G\M\T');
        $time = time() + 3600; // 超时时间

        $queryStringToSign = $this->queryStringToSign('PUT', $time, "/{$bucket}/{$object}");
        $signature = base64_encode(hash_hmac('sha1', $queryStringToSign, $secretAccessKey, true));
        $query = implode('&',[
            'OSSAccessKeyId=' . $accessKeyId,
            'Expires=' . $time,
            'Signature=' . urlencode($signature)
        ]);
        $host = $bucket . '.' . $endpoint;
        $headers = array(
            'Host' => $host,
            'Date' => $date,
            'Content-Type' => mime_content_type($localFile),
            'Content-Length' => filesize($localFile)
        );

        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, 'https://' . $host . '/' . $object. '?' . $query);
        curl_setopt($ch, CURLOPT_PUT, true);
        curl_setopt($ch, CURLOPT_INFILE, fopen($localFile, 'rb'));
        curl_setopt($ch, CURLOPT_INFILESIZE, filesize($localFile));
        curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);

        $response = curl_exec($ch);
        $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
        curl_close($ch);
        if ($httpCode == 200) {

            return [
                'path' => $object,
                'url' => 'https://cdncache.mapleleaf.cn/' . $object
            ];
        } else {
            return '';
        }
    }
    function queryStringToSign($method, $time, $canonicalizedResource)
    {
        return $method . "\n" . "\n" . "\n" . $time . "\n"  . $canonicalizedResource;
    }

    function getPate($object)
    {
        $accessKeyId = $this->config['accessKeyId'];
        $secretAccessKey = $this->config['accessKeySecret'];
        $bucket = $this->config['bucket'];
        $endpoint = $this->config['endpoint'];
        $date = gmdate('D, d M Y H:i:s \G\M\T');
        $time = time() + 3600; // 超时时间
        $queryStringToSign = $this->queryStringToSign('GET', $time, "/{$bucket}/{$object}");
        $signature = base64_encode(hash_hmac('sha1', $queryStringToSign, $secretAccessKey, true));
        $query = implode('&',[
            'OSSAccessKeyId=' . $accessKeyId,
            'Expires=' . $time,
            'Signature=' . urlencode($signature)
        ]);
        $host = $bucket . '.' . $endpoint;
        $url = 'https://' . $host . '/' . $object. '?' . $query;
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_HEADER, false);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        $response = curl_exec($ch);
        curl_close($ch);
        return $url;
    }

}
