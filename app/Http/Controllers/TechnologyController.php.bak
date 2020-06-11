<?php

namespace App\Http\Controllers;

use GuzzleHttp\Client;
use GuzzleHttp\Exception\ClientException;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Validator;

class TechnologyController extends Controller
{
    /**
     * Create a new controller instance.
     *
     * @return void
     */
    public function __construct()
    {
        $this->client = new Client();
        $this->technology = ['magento' => 0];
    }

    public function checkTechnology(Request $request)
    {
        $validator = Validator::make($request->all(), [
            'website_url' => 'required',
        ]);

        if ($validator->fails()) {
            return array(
                'error' => true,
                'message' => $validator->errors()->all(),
            );
        }

        try {
            $response = $this->client->get($request->website_url);
            $html = $response->getBody();
            $headers = $response->getHeaders();

            if (strpos($html, 'skin/frontend') !== false) {
                $this->technology['magento'] += 1;
            }
            if (strpos($html, 'text/x-magento-init') !== false) {
                $this->technology['magento'] += 1;
            }
        } catch (ClientException $exception) {
            $exception_content = $exception->getResponse()->getBody(true);
        }
        try {
            $res = $this->client->get($request->website_url . "/install.php");
            $tmp_html = $res->getBody()->getContents();
            if (strpos($tmp_html, 'ERROR: Magento is already installed') !== false) {
                $this->technology['magento'] += 20;
            }
        } catch (ClientException $exception) {
            $exception_content = $exception->getResponse()->getBody(true);
        }

        $f_result = array_keys($this->technology, min($this->technology));
        if($this->technology[$f_result[0]] > 0){
            $final_result = ucwords($f_result[0]) . " is the technology.";
        }else{
            $final_result = "Unable to recognize technology.";
        }

        return response()->json(["website_url" => $request->website_url, "result" => $final_result], 200);
    }
}
